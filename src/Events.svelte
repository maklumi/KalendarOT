<script>
  export let items = []
</script>

<div>
  <p>Events:</p>
  {#each items as { summary, id } (id)}
    <p>{summary}</p>
  {/each}
</div>
