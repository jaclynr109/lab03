<script>
  import * as d3 from 'd3';

  export let data = [];
  export let title = '';

  let width = 760;
  let height = 280;

  let margin = { top: 50, right: 180, bottom: 55, left: 95 };
  let innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  let xAxis;
  let yAxis;

  $: maxValue = d3.max(data, d => d.value) || 1;

  $: xScale = d3.scaleLinear()
    .domain([0, maxValue])
    .range([0, innerWidth]);

  $: yScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerHeight])
    .padding(0.2);

  $: colorScale = d3.scaleOrdinal(d3.schemeTableau10)
    .domain(data.map(d => d.label));

  $: maxBars = data.filter(d => d.value === maxValue);

  $: annotationText =
    maxBars.length > 1
      ? `${maxBars.map(d => d.label).join(' and ')} are tied for the most lines`
      : maxBars.length === 1
        ? `${maxBars[0].label} has the most lines of code`
        : '';

  $: annotationX = xScale(maxValue) + 10;
  $: annotationY = maxBars.length > 0
    ? yScale(maxBars[0].label) + yScale.bandwidth() / 2 + 3
    : 0;

  $: if (xAxis && yAxis && data.length > 0) {
    d3.select(xAxis).call(
      d3.axisBottom(xScale)
        .ticks(Math.min(d3.max(data, d => d.value), 10))
        .tickFormat(d3.format('d'))
    );

    d3.select(yAxis).call(d3.axisLeft(yScale));
  }
</script>

<div class="container">
  <svg viewBox="0 0 {width} {height}">
    <text
      x={margin.left + innerWidth / 2}
      y={24}
      text-anchor="middle"
      class="chart-title"
    >
      {title}
    </text>

    <g
      transform="translate({margin.left}, {margin.top + innerHeight})"
      bind:this={xAxis}
    ></g>

    <g
      transform="translate({margin.left}, {margin.top})"
      bind:this={yAxis}
    ></g>

    <g transform="translate({margin.left}, {margin.top})">
      {#each data as d}
        <rect
          x={0}
          y={yScale(d.label)}
          width={xScale(d.value)}
          height={yScale.bandwidth()}
          fill={colorScale(d.label)}
        ></rect>
      {/each}

      {#each maxBars as maxBar}
        <rect
          x={0}
          y={yScale(maxBar.label)}
          width={xScale(maxBar.value)}
          height={yScale.bandwidth()}
          fill="none"
          stroke="black"
          stroke-width="2"
        ></rect>
      {/each}

      {#if maxBars.length > 0}
        <text
          x={annotationX}
          y={annotationY}
          text-anchor="start"
          class="annotation"
        >
          {annotationText}
        </text>
      {/if}

      <text
        x={innerWidth / 2}
        y={innerHeight + 42}
        text-anchor="middle"
        class="axis-label"
      >
        Lines of Code
      </text>

      <text
        x={-(innerHeight / 2)}
        y={-62}
        text-anchor="middle"
        transform="rotate(-90)"
        class="axis-label"
      >
        <tspan x={-(innerHeight / 2)} dy="0">Programming</tspan>
        <tspan x={-(innerHeight / 2)} dy="1.1em">Language</tspan>
      </text>
    </g>
  </svg>

  <ul class="legend">
    {#each data as d}
      <li style="--color: {colorScale(d.label)}">
        <span class="swatch"></span>
        <span>{d.label} <em>({d.value})</em></span>
      </li>
    {/each}
  </ul>
</div>

<style>
  svg {
    width: 100%;
    max-width: 760px;
    height: auto;
    overflow: visible;
  }

  .container {
    display: flex;
    gap: 1.5rem;
    align-items: flex-start;
  }

  .legend {
    list-style: none;
    padding: 0;
    margin: 6rem 0 0 0;
    display: grid;
    gap: 0.5rem;
    min-width: 150px;
  }

  .legend li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.9rem;
  }

  .swatch {
    width: 12px;
    height: 12px;
    background-color: var(--color);
    flex: 0 0 auto;
  }

  em {
    color: #666;
    font-style: normal;
  }

  .chart-title {
    font-size: 1rem;
    font-weight: 700;
  }

  .axis-label {
    font-size: 0.8rem;
  }

  .annotation {
    font-size: 0.68rem;
    font-style: italic;
    fill: #333;
  }
</style>