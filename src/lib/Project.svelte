<script>
    export let data = {};
  </script>
  
  <article>
    <h2>{data.title}</h2>
  
    {#if data.image}
      <img src={data.image} alt={data.title} />
    {/if}
  
    {#if data.description}
      <p>{data.description}</p>
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
  </style>