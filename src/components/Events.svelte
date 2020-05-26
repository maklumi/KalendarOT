<script>
  import dayjs from 'dayjs'
  import Cards from './Cards.svelte'

  //todo load date store to set selected date

  export let calendar = undefined
  export let formattedSelected

  let items = []
  const calendarId = '4lf98b4c26fdf729phjoo1omik@group.calendar.google.com'
  $: if (formattedSelected) {
    // console.log(calendar, 'events')
    if (calendar) {
      items = []
      senarai()
    }
  }

  function senarai() {
    const tahun = parseInt(formattedSelected.substring(6, 10))
    const bulan = parseInt(formattedSelected.substring(3, 5)) - 1
    const hari = parseInt(formattedSelected.substring(0, 2))
    console.log(`list for ${hari}-${bulan + 1}-${tahun}`)

    calendar.events
      .list({
        calendarId: calendarId,
        timeMin: new Date(tahun, bulan, hari, 0, 0, 0).toISOString(),
        timeMax: new Date(tahun, bulan, hari, 23, 59, 0).toISOString(),
        showDeleted: false,
        singleEvents: true,
        maxResults: 10,
        orderBy: 'startTime',
      })
      .then((response) => {
        console.log(response.result.items)

        let results = response.result.items
        results.forEach((element) => {
          const desc = element.description + 'OP ' + element.summary
          items = [desc, ...items]
        })
      })
  }
</script>

<p>List for {formattedSelected}</p>
<Cards {items} />
