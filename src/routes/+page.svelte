<script>
  import { onMount } from "svelte";

  let githubData = null;
  let loading = true;
  let error = null;

  onMount(async () => {
    try {
      console.log("Page has been mounted!");

      let response = await fetch("https://api.github.com/users/jaclynr109");
      console.log(response);

      githubData = await response.json();
      console.log(githubData);

    } catch (err) {
      error = err;
    }

    loading = false;
  });

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

{#if loading}
  <p>Loading...</p>
{:else if error}
  <p>Something went wrong: {error.message}</p>
{:else}
  <section class="github-stats">
    <h2>My GitHub Stats</h2>

    <dl>
      <dt>Followers</dt>
      <dd>{githubData.followers}</dd>

      <dt>Following</dt>
      <dd>{githubData.following}</dd>

      <dt>Public Repositories</dt>
      <dd>{githubData.public_repos}</dd>

      <dt>Public Gists</dt>
      <dd>{githubData.public_gists}</dd>
    </dl>
  </section>
{/if}


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
  .github-stats {
  margin: 2rem 0;
  }

  .github-stats dl {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1rem;
    text-align: center;
  }

  .github-stats dt {
    grid-row: 1;
    font-weight: bold;
  }

  .github-stats dd {
    grid-row: 2;
    margin: 0;
    font-size: 1.4rem;
  }
</style>