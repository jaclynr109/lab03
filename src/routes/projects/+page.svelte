<script>
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import Bar from "$lib/Bar.svelte";
  import * as d3 from "d3";

  let years = projects.map(proj => proj.year);
  let range = Math.max(...years) - Math.min(...years);

  $: barData = d3.rollups(projects, v => v.length, d => d.year)
    .map(([year, count]) => ({ label: String(year), value: count }))
    .sort((a, b) => +a.label - +b.label);
</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

<h1>{projects.length} Projects over {range} Years</h1>

<p>
  Scroll down to see a timeline of my projects and how they've contributed to my
  professional and personal life.
</p>

<ProjectNarrative />

<p class="outro">
  Thanks for scrolling through my project story! Feel free to explore all of the
  projects at your leisure below.
</p>

<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>

<section class="chart-section">
  <Bar data={barData} />
</section>

<style>
  .outro {
    margin-bottom: 3rem;
  }

  .chart-section {
    margin: 5rem 0 3rem 0;
  }
</style>