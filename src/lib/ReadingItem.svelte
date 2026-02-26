<script>
  export let data = {};
</script>

<article class="reading-item">
  {#if data.image}
    <img class="cover" src={data.image} alt={`Cover of ${data.title}`} />
  {/if}

  <div class="details">
    <h3 class="title">{data.title}</h3>
    <p class="author">{data.author}</p>

    {#if data.year}
      <p class="meta">{data.year}</p>
    {/if}

    {#if data.description}
      <p class="description">{data.description}</p>
    {/if}

    {#if data.link}
      <p class="link">
        <a href={data.link} target="_blank" rel="noreferrer">Learn more →</a>
      </p>
    {/if}
  </div>
</article>

<style>
  .reading-item {
    display: flex;
    gap: 1.25rem;
    align-items: flex-start;
    padding: 1rem 0;
    border-bottom: 1px solid oklch(85% 2% 200);
  }

  .cover {
    width: 90px;
    height: auto;
    border-radius: 6px;
    flex-shrink: 0;
  }

  .details {
    min-width: 0;
  }

  .title {
    margin: 0;
    font-size: 1.35rem;
    font-weight: 700;
  }

  .author {
    margin: 0.25rem 0 0.25rem;
    font-weight: 600;
  }

  .meta {
    margin: 0;
    opacity: 0.7;
  }

  .description {
    margin-top: 0.75rem;
    max-width: 60ch;
  }

  .link {
    margin-top: 0.75rem;
  }

  .link a {
    color: var(--color-accent);
    text-decoration: none;
    font-weight: 600;
  }

  .link a:hover {
    text-decoration: underline;
  }
</style>