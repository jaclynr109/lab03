<script>
  import { base } from '$app/paths';
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';
  import {
    computePosition,
    autoPlacement,
    offset
  } from '@floating-ui/dom';
  import LineChart from '$lib/LineChart.svelte';

  let width = 700, height = 350;

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
  let commitTooltip;

  let hoveredIndex = -1;
  let tooltipPosition = { x: 0, y: 0 };
  let clickedCommits = [];

  let svg;

  let linesByDate = [];

  $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

  $: colorScale = d3.scaleLinear()
    .domain([0, 12, 24])
    .range(['#1f3b73', '#f4a261', '#1f3b73']);

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line: Number(row.line),
      length: Number(row.length),
      depth: Number(row.depth),
      date: new Date(row.date + 'T00:00' + row.timezone),
      datetime: new Date(row.datetime)
    }));

    commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let { author, date, time, timezone, datetime } = first;

      return {
        id: commit,
        url: 'https://github.com/jaclynr109/lb03/commit/' + commit,
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

    commits = d3.sort(commits, d => -d.totalLines);
  });

  $: {
  if (!locData.length) {
      linesByDate = [];
    } else {
      let rolled = d3.rollups(
        locData,
        (values) => values.length,
        (d) => d3.timeDay.floor(d.datetime)
      ).map(([date, count]) => ({ date, count }));

      let [minDate, maxDate] = d3.extent(rolled, (d) => d.date);

      let allDays = d3.timeDays(minDate, d3.timeDay.offset(maxDate, 1));

      linesByDate = allDays.map((date) => ({
        date,
        count: rolled.find((d) => +d.date === +date)?.count ?? 0
      }));
    }
  }
  async function dotInteraction(index, evt) {
    let hoveredDot = evt.target;

    if (evt.type === 'mouseenter') {
      hoveredIndex = index;

      tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
        strategy: 'fixed',
        middleware: [
          offset(5),
          autoPlacement()
        ]
      });
    } else if (evt.type === 'mouseleave') {
      hoveredIndex = -1;
    } else if (evt.type === 'click') {
      let commit = commits[index];

      if (!clickedCommits.includes(commit)) {
        clickedCommits = [...clickedCommits, commit];
      } else {
        clickedCommits = clickedCommits.filter(c => c !== commit);
      }
    }
  }

  $: brushSelection = null;

  function brushed (evt) {
    brushSelection = evt.selection;
  }

  function isCommitBrushed(commit) {
    if (!brushSelection) {
      return false;
    }

    let min = {
      x: brushSelection[0][0],
      y: brushSelection[0][1]
    };

    let max = {
      x: brushSelection[1][0],
      y: brushSelection[1][1]
    };

    let x = xScale(commit.datetime);
    let y = yScale(commit.hourFrac);

    return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
  }

  $: brushedCommits = brushSelection ? commits.filter(isCommitBrushed) : [];

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

  $: rScale = d3.scaleSqrt()
    .domain(minLines && maxLines ? [minLines, maxLines] : [1, 1])
    .range([5, 30]);

  $: selectedLines =
    clickedCommits.length > 0 ? clickedCommits.flatMap(d => d.lines) : locData;

  $: selectedCounts = d3.rollup(
    selectedLines,
    v => v.length,
    d => d.type
  );

  $: allTypes = Array.from(new Set(locData.map(d => d.type)));

  $: barData = allTypes.map(type => ({
    label: String(type),
    value: selectedCounts.get(type) || 0
  }));

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

  $: if (svg) {
    d3.select(svg)
      .call(
        d3.brush()
          .extent([
            [usableArea.left, usableArea.top],
            [usableArea.right, usableArea.bottom]
          ])
          .on('brush end', brushed)
      );

    d3.select(svg).selectAll('.dots, .overlay ~ *').raise();
  }

  $: selectedCommits = Array.from(new Set([...clickedCommits, ...brushedCommits]));

	$: selectedLines = (selectedCommits.length > 0 ? selectedCommits : commits).flatMap(d => d.lines);


</script>

<h3>Commits by time of day</h3>

<div class="chart-layout">
  <dl
    class="info tooltip"
    bind:this={commitTooltip}
    hidden={hoveredIndex === -1}
    style:left={tooltipPosition.x + 'px'}
    style:top={tooltipPosition.y + 'px'}
  >
    <dt>Commit</dt>
    <dd><a href={hoveredCommit.url} target="_blank" rel="noreferrer">{hoveredCommit.id}</a></dd>

    <dt>Author</dt>
    <dd>{hoveredCommit.author}</dd>

    <dt>Date</dt>
    <dd>{hoveredCommit.datetime?.toLocaleString('en', { dateStyle: 'full' })}</dd>

    <dt>Time</dt>
    <dd>{hoveredCommit.datetime?.toLocaleString('en', { timeStyle: 'short' })}</dd>

    <dt>Lines</dt>
    <dd>{hoveredCommit.totalLines}</dd>
  </dl>

  <svg bind:this={svg} viewBox="0 0 {width} {height}">
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
          class:selected={ selectedCommits.includes(commit) }
          on:mouseenter={(evt) => dotInteraction(index, evt)}
          on:mouseleave={(evt) => dotInteraction(index, evt)}
          on:click={(evt) => dotInteraction(index, evt)}
        />
      {/each}
    </g>
  </svg>
</div>

<BarHorizontal
  data={barData}
  title={selectedCommits.length > 0
    ? `Lines of Code: ${selectedCommits.length} Selected Commits`
    : 'Website Breakdown'}

/>

<LineChart data={linesByDate} />

<style>
  .chart-layout {
    position: relative;
  }

  svg {
    overflow: visible;
  }

  dl.info {
    display: grid;
    grid-template-columns: max-content 1fr;
    gap: 0.35em 1em;
    margin: 0;
    align-content: start;
    transition-duration: 500ms;
    transition-property: opacity, visibility;
  }

  dl.info dt {
    margin: 0;
    color: #777;
    font-weight: 400;
  }

  dl.info dd {
    margin: 0;
  }

  dl.info[hidden]:not(:hover, :focus-within) {
    opacity: 0;
    visibility: hidden;
  }

  .tooltip {
    position: fixed;
    z-index: 10;
    background-color: oklch(100% 0 0 / 0.85);
    border-radius: 10px;
    padding: 1rem;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
    backdrop-filter: blur(6px);
    pointer-events: none;
  }

  .gridlines {
    stroke-opacity: 0.3;
  }

  .gridlines line {
    stroke-width: 1.5;
  }

  circle {
    transition: 200ms;
  }

  .dots:hover circle {
    opacity: 0.3;
  }

  .dots circle:hover {
    opacity: 1;
    fill: darkgreen;
  }

  .selected {
    fill: var(--color-accent);
  }
</style>