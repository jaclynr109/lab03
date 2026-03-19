<script>
  import { base } from '$app/paths';
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';

  let locData = [];
  let barData = [];

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line: Number(row.line),
      length: Number(row.length),
      depth: Number(row.depth)
    }));

    barData = d3.rollups(
      locData,
      v => v.length,
      d => d.type
    )
      .map(([type, count]) => ({
        label: String(type),
        value: count
      }))
      .sort((a, b) => b.value - a.value);
  });
</script>

<svelte:head>
  <title>Meta</title>
</svelte:head>

<h1>Meta</h1>
<p>Meta page to visualize project data.</p>

{#if barData.length > 0}
  <BarHorizontal data={barData} />
{/if}