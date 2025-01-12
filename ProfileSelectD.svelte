<script>
  import { onMount } from 'svelte'
  import { obsD, sendCommandD } from './obs.js'

  let profilesD = []
  let currentProfileD = ''

  onMount(async function () {
    const data = await sendCommandD('GetProfileList')
    profilesD = data.profiles || []
    currentProfileD = data.currentProfileName || ''
  })

  obsD.on('CurrentProfileChanged', async (data) => {
    console.log('CurrentProfileChanged', data.profileName)
    currentProfileD = data.profileName || ''
  })
  obsD.on('ProfileListChanged', async (data) => {
    console.log('ProfileListChanged', data.profiles.length)
    profilesD = data.profiles || []
  })

  async function setCurrentProfileD (event) {
    sendCommandD('SetCurrentProfile', { profileName: event.target.value })
  }
</script>

<!-- <div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentProfileD} title="Change Profile" on:change={setCurrentProfileD}>
  {#each profilesD as profile}
    <option value={profile}>{profile}</option>
  {/each}
  </select>
</div> -->

<button class="button is-info is-dark" disabled style="margin: 0 .5rem .5rem 0;">
  <span>{currentProfileD}</span>
</button>