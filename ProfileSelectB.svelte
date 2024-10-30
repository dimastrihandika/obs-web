<script>
  import { onMount } from 'svelte'
  import { obsB, sendCommandB } from './obs.js'

  let profilesB = []
  let currentProfileB = ''

  onMount(async function () {
    const data = await sendCommandB('GetProfileList')
    profilesB = data.profiles || []
    currentProfileB = data.currentProfileName || ''
  })

  obsB.on('CurrentProfileChanged', async (data) => {
    console.log('CurrentProfileChanged', data.profileName)
    currentProfileB = data.profileName || ''
  })
  obsB.on('ProfileListChanged', async (data) => {
    console.log('ProfileListChanged', data.profiles.length)
    profilesB = data.profiles || []
  })

  async function setCurrentProfileB (event) {
    sendCommandB('SetCurrentProfile', { profileName: event.target.value })
  }
</script>

<div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentProfileB} title="Change Profile" on:change={setCurrentProfileB}>
  {#each profilesB as profile}
    <option value={profile}>{profile}</option>
  {/each}
  </select>
</div>
