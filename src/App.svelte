<script>
  import cred from './credential'
  import Events from './Events.svelte'

  let googleapi = undefined
  let items = undefined
  $: console.log(`sudahsignin ${sudahsignin}`)

  // Client ID and API key from the Developer Console
  var CLIENT_ID = cred.CLIENT_ID
  var API_KEY = cred.API_KEY

  // Array of API discovery doc URLs for APIs used by the quickstart
  var DISCOVERY_DOCS = [
    'https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest',
  ]

  // Authorization scopes required by the API; multiple scopes can be
  // included, separated by spaces.
  var SCOPES = 'https://www.googleapis.com/auth/calendar.readonly'

  //url is URL of external file, implementationCode is the code
  //to be called from the file, location is the location to
  //insert the <script> element
  var loadJS = function (url, implementationCode, location) {
    var scriptTag = document.createElement('script')
    scriptTag.src = url
    scriptTag.onload = implementationCode
    scriptTag.onreadystatechange = implementationCode
    location.appendChild(scriptTag)
  }

  var yourCodeToBeCalled = function () {
    handleClientLoad()
  }

  loadJS(
    'https://apis.google.com/js/platform.js',
    yourCodeToBeCalled,
    document.body,
  )

  window.onSignIn = (googleUser) => {
    const profile = googleUser.getBasicProfile()
    console.log('ID: ' + profile.getId())
    console.log('Name: ' + profile.getName())
    console.log('Image URL: ' + profile.getImageUrl())
    console.log('Email: ' + profile.getEmail())
  }

  // var authorizeButton = document.getElementById('authorize_button')
  // var signoutButton = document.getElementById('signout_button')

  let sudahsignin = undefined

  /**
   *  On load, called to load the auth2 library and API client library.
   */
  function handleClientLoad() {
    gapi.load('client:auth2', initClient)
  }

  /**
   *  Initializes the API client library and sets up sign-in state
   *  listeners.
   */
  function initClient() {
    gapi.client
      .init({
        apiKey: API_KEY,
        clientId: CLIENT_ID,
        discoveryDocs: DISCOVERY_DOCS,
        scope: SCOPES,
      })
      .then(
        function () {
          // Listen for sign-in state changes.
          gapi.auth2.getAuthInstance().isSignedIn.listen(updateSigninStatus)

          // Handle the initial sign-in state.
          sudahsignin = gapi.auth2.getAuthInstance().isSignedIn.get()
          updateSigninStatus(sudahsignin)
        },
        function (error) {
          appendPre(JSON.stringify(error, null, 2))
          console.log(error)
        },
      )
  }

  /**
   *  Called when the signed in status changes, to update the UI
   *  appropriately. After a sign-in, the API is called.
   */
  function updateSigninStatus(isSignedIn) {
    sudahsignin = isSignedIn
    if (isSignedIn) {
      // listUpcomingEvents()
      senarai()
    }
  }

  /**
   *  Sign in the user upon button click.
   */
  function handleAuthClick(event) {
    gapi.auth2.getAuthInstance().signIn()
  }

  /**
   *  Sign out the user upon button click.
   */
  function handleSignoutClick(event) {
    gapi.auth2.getAuthInstance().signOut()
  }

  function senarai() {
    gapi.client.calendar.events
      .list({
        calendarId: 'primary',
        timeMin: new Date().toISOString(),
        showDeleted: false,
        singleEvents: true,
        maxResults: 10,
        orderBy: 'startTime',
      })
      .then((response) => {
        items = response.result.items
        console.log(items)
      })
  }
</script>

<main>

  <nav>

    {#if sudahsignin === 'undefined'}
      <p>loading...</p>
    {:else if sudahsignin}
      <button on:click={handleSignoutClick}>Sign Out</button>
    {:else}
      <button on:click={handleAuthClick}>Authorize</button>
    {/if}

  </nav>

  <Events {items} />
</main>

<!-- 

  chrome://flags/#same-site-by-default-cookies
 -->
