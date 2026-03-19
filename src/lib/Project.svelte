<script>
  import { base } from '$app/paths';
  export let data = {};
</script>

<article>
  <h2>{data.title}</h2>

  {#if data.image}
    <img src={`${base}${data.image}`} alt={data.title} />
  {/if}

  {#if data.description || data.year}
    <p>
      {#if data.year}
        <strong>{data.year}</strong>{#if data.description} | {/if}
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
  }
</style>