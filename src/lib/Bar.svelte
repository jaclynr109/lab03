<script>
  import * as d3 from 'd3';

  export let data = [];

  let width = 650;
  let height = 420;

  let margin = { top: 80, right: 40, bottom: 90, left: 80 };
  let innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  let xAxis;
  let yAxis;

  let selectedIndex = -1;

  let liveText = "";

      let showChart = true;

    function toggleView() {
        showChart = !showChart;
        liveText = showChart ? "Bar chart view shown." : "Table view shown.";
    }


  $: description = `A bar chart showing project counts by year. ${data
    .map(d => `${d.label}: ${d.value} projects`)
    .join(', ')}. ${annotationText}`;

  $: xScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerWidth])
    .padding(0.2);

  $: maxValue = d3.max(data, d => d.value) || 1;

  $: yScale = d3.scaleLinear()
    .domain([0, maxValue])
    .range([innerHeight, 0]);

  $: colorScale = d3.scaleOrdinal()
    .domain(data.map(d => d.label))
    .range(d3.quantize(d3.interpolateBlues, data.length));

  $: maxBars = data.filter(d => d.value === maxValue);

  $: annotationText =
    maxBars.length > 1
      ? `${maxBars.map(d => d.label).join(' and ')} are tied for the most projects`
      : maxBars.length === 1
        ? `${maxBars[0].label} has the most projects`
        : '';

  $: tieCenterX =
    maxBars.length > 0
      ? (
          (xScale(maxBars[0].label) + xScale.bandwidth() / 2) +
          (xScale(maxBars[maxBars.length - 1].label) + xScale.bandwidth() / 2)
        ) / 2
      : 0;

  $: if (xAxis && yAxis && data.length > 0) {
    d3.select(xAxis).call(d3.axisBottom(xScale));

    d3.select(yAxis).call(
      d3.axisLeft(yScale)
        .tickFormat(d => Number.isInteger(d) ? d : '')
        .tickValues(d3.range(0, maxValue + 1))
    );
  }

  function toggleBar(index, event) {
    if (!event.key || event.key === "Enter" || event.key === " ") {
      selectedIndex = index;

      const d = data[index];
      liveText = `${d.label}: ${d.value} projects selected.`;
    }
  }
</script>

<button
  on:click={toggleView}
  aria-pressed={!showChart}
  aria-label="Toggle between bar chart and table view"
  class="toggle-button">
    {showChart ? 'Show Table' : 'Show Chart'}
</button>

{#if showChart}
  <div class="container">
    <svg
      viewBox={`0 0 ${width} ${height}`}
      role="img"
      aria-labelledby="bar-title bar-desc"
    >
      <title id="bar-title">Projects by Year</title>
      <desc id="bar-desc">{description}</desc>

      <g
        transform="translate({margin.left}, {margin.top + innerHeight})"
        bind:this={xAxis}
      ></g>

      <g
        transform="translate({margin.left}, {margin.top})"
        bind:this={yAxis}
      ></g>

      <g transform={`translate(${margin.left}, ${margin.top})`}>
        {#each data as d, index}
          <rect
            x={xScale(d.label)}
            y={yScale(d.value)}
            width={xScale.bandwidth()}
            height={innerHeight - yScale(d.value)}
            fill={colorScale(d.label)}
            stroke="black"
            opacity={selectedIndex === -1 || selectedIndex === index ? 1 : 0.45}
            tabindex="0"
            role="button"
            aria-label={`Select ${d.label} with ${d.value} projects`}
            stroke-width={selectedIndex === index ? 3 : 1}
            on:click={(e) => toggleBar(index, e)}
            on:keyup={(e) => toggleBar(index, e)}
          />
        {/each}

        {#each maxBars as maxBar}
          <rect
            x={xScale(maxBar.label)}
            y={yScale(maxBar.value)}
            width={xScale.bandwidth()}
            height={innerHeight - yScale(maxBar.value)}
            fill="none"
            stroke="black"
            stroke-width="2.5"
          ></rect>
        {/each}

        {#if maxBars.length > 0}
          {#each maxBars as maxBar}
            <line
              x1={xScale(maxBar.label) + xScale.bandwidth() / 2}
              y1={yScale(maxBar.value)}
              x2={tieCenterX}
              y2={yScale(maxValue) - 30}
              stroke="black"
              stroke-width="1.2"
            ></line>
          {/each}

          <text
            x={tieCenterX}
            y={yScale(maxValue) - 35}
            text-anchor="middle"
            class="annotation"
          >
            {annotationText}
          </text>
        {/if}

        <text
          x={innerWidth / 2}
          y={innerHeight + 60}
          text-anchor="middle"
          class="axis-label"
        >
          Year
        </text>

        <text
          x={-(innerHeight / 2)}
          y={-55}
          text-anchor="middle"
          transform="rotate(-90)"
          class="axis-label"
        >
          Number of Projects
        </text>
      </g>
    </svg>

    <p aria-live="polite" class="sr-only">{liveText}</p>

    <ul class="legend">
      {#each data as d}
        <li style="--color: {colorScale(d.label)}">
          <span class="swatch"></span>
          <span>{d.label} <em>({d.value})</em></span>
        </li>
      {/each}
    </ul>
  </div>
{:else}
  <table aria-label="Table showing project counts by year" class="data-table">
      <caption>Projects by Year</caption>
      <thead>
          <tr>
            <th id="year-header" scope="col">Year</th>
            <th id="projects-header" scope="col">Projects</th>
          </tr>
        </thead>
      <tbody>
          {#each data as d, i}
            <tr>
              <th id="row-{i}" scope="row">{d.label}</th>
              <td aria-labelledby="row-{i} projects-header">{d.value}</td>
            </tr>
          {/each}
        </tbody>
    </table>
    {/if}


<style>
  rect:focus-visible {
    stroke: white;
    stroke-width: 2px;
    stroke-dasharray: 4; /* Adjust the dash length as needed */
    }
  .data-table {
    margin-top: 1rem;
    margin-bottom: 1rem;
    border-collapse: collapse;
    width: 100%;
    max-width: 30em;
  }

  .data-table caption {
    font-weight: bold;
    margin-bottom: 0.5em;
    text-align: left;
  }

  .data-table th,
  .data-table td {
    border: 1px solid #ccc;
    padding: 0.5em;
    text-align: left;
  }

  .data-table th {
    background-color: #f0f0f0;
  }
  .sr-only {
    position: absolute;
    left: -9999px;
    width: 1px;
    height: 1px;
    overflow: hidden;
  }
  rect {
    transition: 300ms;
    outline: none;
  }

  svg:hover rect:not(:hover),
  .container:focus-within rect:not(:focus-visible) {
    opacity: 50%;
  }

  rect:focus-visible {
    stroke: white;
    stroke-width: 2px;
    stroke-dasharray: 4;
  }
  svg {
    width: 100%;
    max-width: 700px;
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
    margin: 0;
    display: grid;
    gap: 0.75rem;
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
  }

  em {
    color: #666;
    font-style: normal;
  }

  .chart-title {
    font-size: 1.1em;
    font-weight: bold;
  }

  .axis-label {
    font-size: 0.9em;
  }

  .annotation {
    font-size: 0.85em;
    font-style: italic;
  }
</style>