<script>
  import { base } from '$app/paths';
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let width = 1000, height = 600;

  let margin = { top: 20, right: 20, bottom: 30, left: 50 };

  let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left
  };
  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;

  let locData = [];
  let commits = [];

  let xAxis;
  let yAxis;
  let yAxisGridlines;

  $: colorScale = d3.scaleLinear()
    .domain([0, 12, 24])
    .range(["#1f3b73", "#f4a261", "#1f3b73"]);

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line: Number(row.line),
      length: Number(row.length),
      depth: Number(row.depth),
      date: new Date(row.date + "T00:00" + row.timezone),
      datetime: new Date(row.datetime)
    }));

    commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let { author, date, time, timezone, datetime } = first;

      return {
        id: commit,
        url: "https://github.com/jaclynr109/lb03/commit/" + commit,
        author,
        date,
        time,
        timezone,
        datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length,
        lines
      };
    });
  });

  $: [minDate, maxDate] = d3.extent(commits.map(d => d.date));

  $: maxDatePlusOne = maxDate ? new Date(maxDate) : null;
  $: if (maxDatePlusOne) {
    maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);
  }

  $: xScale = d3.scaleTime()
    .domain(minDate && maxDatePlusOne ? [minDate, maxDatePlusOne] : [new Date(), new Date()])
    .range([usableArea.left, usableArea.right])
    .nice();

  $: yScale = d3.scaleLinear()
    .domain([24, 0])
    .range([usableArea.bottom, usableArea.top]);

  $: [minLines, maxLines] = d3.extent(commits.map(d => d.totalLines));

  $: rScale = d3.scaleLinear()
    .domain(minLines && maxLines ? [minLines, maxLines] : [1, 1])
    .range([5, 30]);

  $: if (xAxis && xScale) {
    d3.select(xAxis).call(d3.axisBottom(xScale));
  }

  $: if (yAxis && yScale) {
    d3.select(yAxis).call(
      d3.axisLeft(yScale)
        .tickFormat(d => String(d % 24).padStart(2, '0') + ':00')
    );
  }

  $: if (yAxisGridlines && yScale) {
    const axis = d3.axisLeft(yScale)
      .tickFormat('')
      .tickSize(-usableArea.width);

    d3.select(yAxisGridlines).call(axis);

    d3.select(yAxisGridlines)
      .selectAll('.tick line')
      .attr('stroke', d => colorScale(d));
  }
</script>

<h3>Commits by time of day</h3>

<svg viewBox="0 0 {width} {height}">
  <g
    class="gridlines"
    transform="translate({usableArea.left}, 0)"
    bind:this={yAxisGridlines}
  />

  <g
    transform="translate({usableArea.left}, 0)"
    bind:this={yAxis}
  />

  <g
    transform="translate(0, {usableArea.bottom})"
    bind:this={xAxis}
  />

  <g class="dots">
    {#each commits as commit, index}
      <circle
        cx={xScale(commit.datetime)}
        cy={yScale(commit.hourFrac)}
        r={rScale(commit.totalLines)}
        fill="steelblue"
        fill-opacity="0.7"
      />
    {/each}
  </g>
</svg>

<style>
  svg {
    overflow: visible;
  }

  .gridlines {
    stroke-opacity: 0.3;
  }

  .gridlines line {
    stroke-width: 1.5;
  }
</style>