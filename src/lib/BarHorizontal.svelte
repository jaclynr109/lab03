<script>
  import * as d3 from 'd3';

  export let data = [];
  export let title = '';

  let width = 820;
  let height = 420;

  let margin = { top: 80, right: 180, bottom: 70, left: 110 };
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

  $: annotationX = xScale(maxValue) + 12;
  $: annotationY = maxBars.length > 0
    ? yScale(maxBars[0].label) + yScale.bandwidth() / 2 + 4
    : 0;

  $: if (xAxis && yAxis && data.length > 0) {
    d3.select(xAxis).call(
      d3.axisBottom(xScale)
        .ticks(6)
        .tickFormat(d3.format('d'))
    );

    d3.select(yAxis).call(d3.axisLeft(yScale));
  }
</script>

<div class="container">
  <svg viewBox="0 0 {width} {height}">
    <text
      x={margin.left + innerWidth / 2}
      y={30}
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
          stroke-width="2.5"
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
        y={innerHeight + 50}
        text-anchor="middle"
        class="axis-label"
      >
        Lines of Code
      </text>

      <text
        x={-(innerHeight / 2)}
        y={-75}
        text-anchor="middle"
        transform="rotate(-90)"
        class="axis-label"
      >
        Language
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
    max-width: 860px;
    height: auto;
    overflow: visible;
  }

  .container {
    display: flex;
    gap: 2rem;
    align-items: flex-start;
  }

  .legend {
    list-style: none;
    padding: 0;
    margin: 10rem 0 0 0;
    display: grid;
    gap: 0.75rem;
    min-width: 170px;
  }

  .legend li {
    display: flex;
    align-items: center;
    gap: 0.5rem;
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
    font-size: 1.1em;
    font-weight: 700;
  }

  .axis-label {
    font-size: 0.9em;
  }

  .annotation {
    font-size: 0.7em;
    font-style: italic;
    fill: #333;
  }
</style>