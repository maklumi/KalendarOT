<script>
  import Datepicker from 'svelte-calendar'
  import dayjs from 'dayjs'

  let currentyear = dayjs(new Date())
  let lastyear = currentyear.subtract(1, 'year').toDate()
  let nextyear = currentyear.add(1, 'year').toDate()

  export let formattedSelected
</script>

<Datepicker
  class="datepick"
  start={lastyear}
  end={nextyear}
  bind:formattedSelected
  format={(date) => dayjs(date).format('DD/MM/YYYY')} />
