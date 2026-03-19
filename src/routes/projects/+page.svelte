<script>
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  
  import Bar from '$lib/Bar.svelte';
  // Extract the years from each project
  let years = projects.map(proj => proj.year);

  // Calculate the year range
  let range = Math.max(...years) - Math.min(...years);

  let rawData = [];
  let wrangled = [];
  let percentages = [];

  onMount(async () => {
    rawData = await d3.json('/lab6_example.json');

    wrangled = d3.rollups(
      rawData,
      v => d3.sum(v, d => d.lines),
      d => d.language
    );

    const totalLines = d3.sum(rawData, d => d.lines);

    percentages = d3.rollups(
      rawData,
      v => +(d3.sum(v, d => d.lines) / totalLines * 100).toFixed(1),
      d => d.language
    );
  });
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

<section>
  <h2>Data wrangling result</h2>
  <pre>{JSON.stringify(wrangled, null, 2)}</pre>
</section>

<section>
  <h2>Percent of total lines by language</h2>
  <pre>{JSON.stringify(percentages, null, 2)}</pre>
</section>

<style>
  .outro {
    margin-bottom: 3rem;
  }
</style>

<Bar />