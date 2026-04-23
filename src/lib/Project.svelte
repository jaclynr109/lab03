<script>
  import { base } from '$app/paths';
  export let data = {};
</script>

<article>
  {#if data.url}
    <a href={data.url} target="_blank" rel="noopener noreferrer">
      <h2>{data.title}</h2>
    </a>
  {:else}
    <h2>{data.title}</h2>
  {/if}

  {#if data.image}
    <img src={`${base}${data.image}`} alt={data.title} />
  {/if}

  {#if data.description || data.year}
    <p>
      {#if data.year}
        <strong class="year">{data.year}</strong>
      {/if}

      {#if data.year && data.description}
        <span class="separator">|</span>
      {/if}

      {data.description}
    </p>
  {/if}
</article>

<style>
  article {
    display: grid;
    grid-template-rows: subgrid;
    grid-row: span 3;
  }

  h2 {
    margin: 0;
  }

  img {
    width: 100%;
    height: auto;
    display: block;
  }

  p {
    margin: 0;
    line-height: 1.4;
  }

  .separator {
    margin: 0 0.5rem;
    color: #888; /* subtle polish */
  }

  .year {
    font-weight: 600;
  }
</style>