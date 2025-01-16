<script>
  import { onMount } from 'svelte'
  import { obsC, sendCommandC } from './obs.js'

  let profilesC = []
  let currentProfileC = ''

  onMount(async function () {
    const data = await sendCommandC('GetProfileList')
    profilesC = data.profiles || []
    currentProfileC = data.currentProfileName || ''
  })

  obsC.on('CurrentProfileChanged', async (data) => {
    console.log('CurrentProfileChanged', data.profileName)
    currentProfileC = data.profileName || ''
  })
  obsC.on('ProfileListChanged', async (data) => {
    console.log('ProfileListChanged', data.profiles.length)
    profilesC = data.profiles || []
  })

  async function setCurrentProfileC (event) {
    sendCommandC('SetCurrentProfile', { profileName: event.target.value })
  }
</script>

<!-- <div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentProfileC} title="Change Profile" on:change={setCurrentProfileC}>
  {#each profilesD as profile}
    <option value={profile}>{profile}</option>
  {/each}
  </select>
</div> -->

<button class="button is-info is-dark" disabled style="margin: 0 .5rem .5rem 0;">
  <span>{currentProfileC}</span>
</button>