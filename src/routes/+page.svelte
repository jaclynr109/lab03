<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";

  import reading from "$lib/reading.json";
  import ReadingItem from "$lib/ReadingItem.svelte";
</script>

<h1>Jaclyn Rambarran</h1>

<p>
  I am a graduate student at MIT in the Technology and Policy Program interested
  in decarbonized electric energy systems.
</p>

<img
  src="images/jaclyn-profile.jpeg"
  alt="Portrait of Jaclyn smiling indoors"
  width="300"
/>

<!-- ===================== -->
<!-- Latest Projects       -->
<!-- ===================== -->

<h2>Latest Projects</h2>

<div class="projects highlights">
  {#each projects.slice(0, 3) as p}
    <Project data={p} />
  {/each}
</div>

<!-- ===================== -->
<!-- What I'm Reading      -->
<!-- ===================== -->

<h2>What I’m Reading</h2>

<div class="reading">
  {#each reading as item}
    <ReadingItem data={item} />
  {/each}
</div>

<style>
  .highlights {
    margin-bottom: 3rem;
  }

  .reading {
    display: grid;
    gap: 1.5rem;
    margin-bottom: 3rem;
  }
</style>