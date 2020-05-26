<script>
  export let items = []
  let results = []

  $: createList(items)

  let arr = [
    'OP ',
    'Remarks',
    'Recorded by :',
    'D/W :',
    'Summary :',
    'OPERATION :',
    'INDICATION :',
    'Husband :',
    'Pt   :',
    'Mobile no',
    'NRIC :',
    'Name :',
  ]

  function parser(modtext) {
    let result = []
    for (let i = 0; i < arr.length; i++) {
      const tokens = modtext.split(arr[i])
      modtext = tokens[0]
      result = [...result, tokens[1] || '-']
    }
    return result
  }

  function stripHtml(html) {
    var element = document.createElement('div')
    element.innerHTML = html
    return element.textContent || element.innerText || ''
  }

  function createList(items) {
    results = []
    items.forEach((element) => {
      const removenumber = element.replace(/\s+\d+[\.]\s+/g, ' ')
      const result = parser(stripHtml(removenumber))
      results = [result, ...results]
    })
    // localStorage.setItem('thelist', JSON.stringify(results))
  }
</script>

<style>
  p {
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    border-radius: 5px;
    background: whitesmoke;
    margin: 1rem;
    padding: 1rem;
  }
</style>

{#if results.length === 0}
  <p>No case to list</p>
{/if}
{#each results as result}
  <p>
    {arr[11]}{result[11]} {arr[10]}{result[10]}
    <br />
    {arr[9]}: {arr[8]} {result[8]} {arr[7]} {result[7]}
    <br />
    {arr[6]} {result[4]}
    <br />
    {arr[5]} {result[0]}
    <br />
    {arr[3]} {result[3]}
    <br />
    {arr[2]} {result[2]}
    <br />
    {arr[1]} {result[1]}
  </p>
{/each}
