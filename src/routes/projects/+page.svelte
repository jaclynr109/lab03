<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
</script>

<svelte:head>
  <title>Projects</title>
</svelte:head>

<nav>
  <a href=".">Home</a>
  <a class="current" href="projects">Projects</a>
  <a href="contact">Contact</a>
  <a href="resume">Resume</a>
  <a href="https://github.com/jaclynr109" target="_blank" rel="noreferrer">
    GitHub
  </a>
</nav>

<h1>Projects ({projects.length})</h1>

<div class="projects">
  {#each projects as p}
    <Project data={p} />
  {/each}
</div>