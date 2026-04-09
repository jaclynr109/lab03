<script>
  import * as d3 from 'd3';

  export let data = [];

  let width = 1000, height = 300;

  let margin = { top: 20, right: 20, bottom: 30, left: 50 };

  let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left
  };

  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;

  let xAxis;
  let yAxis;

  $: xScale = d3.scaleTime()
    .domain(d3.extent(data, d => d.date))
    .range([usableArea.left, usableArea.right]);

  $: yScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.count) ?? 0])
    .range([usableArea.bottom, usableArea.top])
    .nice();

  $: line = d3.line()
    .x(d => xScale(d.date))
    .y(d => yScale(d.count))
    .curve(d3.curveBumpX);

  $: if (xAxis && xScale) {
    d3.select(xAxis).call(d3.axisBottom(xScale));
  }

  $: if (yAxis && yScale) {
    d3.select(yAxis).call(d3.axisLeft(yScale));
  }
</script>

<h3>Lines Edited by Day</h3>

<svg viewBox="0 0 {width} {height}">
  <!-- Y axis -->
  <g
    transform="translate({usableArea.left}, 0)"
    bind:this={yAxis}
  />

  <!-- X axis -->
  <g
    transform="translate(0, {usableArea.bottom})"
    bind:this={xAxis}
  />

  <!-- Line -->
  <path
    d={line(data)}
    fill="none"
    stroke="steelblue"
    stroke-width="2"
  />

  <!-- Dots -->
  {#each data as d}
    <circle
      cx={xScale(d.date)}
      cy={yScale(d.count)}
      r="3"
      fill="steelblue"
    />
  {/each}

  <!-- x-axis label -->
  <text
    x={usableArea.left + (usableArea.right - usableArea.left) / 2}
    y={height - 5}
    text-anchor="middle"
    class="axis-label">
    Date
  </text>

  <!-- y-axis label -->
  <text
    x={-(usableArea.top + (usableArea.bottom - usableArea.top) / 2)}
    y={10}
    text-anchor="middle"
    transform="rotate(-90)"
    class="axis-label">
    Number of Lines Edited
  </text>
</svg>

<style>
  h3 {
    text-align: center;
  }

  .axis-label {
    font-size: 0.8em;
    fill: currentColor;
  }

  svg {
    overflow: visible;
  }
</style>