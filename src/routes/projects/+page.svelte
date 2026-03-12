<script>
  import ProjectNarrative from "$lib/ProjectNarrative.svelte";
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";

  // Extract the years from each project
  let years = projects.map(proj => proj.year);

  // Calculate the year range
  let range = Math.max(...years) - Math.min(...years);
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

<style>
  .outro {
    margin-bottom: 3rem;
  }
</style>