<script>
  import { onMount } from 'svelte'
  import { obsD, sendCommandD } from './obs.js'

  let collectionsD = []
  let currentCollectionD = ''

  onMount(async function () {
    const data = await sendCommandD('GetSceneCollectionList')
    collectionsD = data.sceneCollections || []
    currentCollectionD = data.currentSceneCollectionName || ''
  })

  obsD.on('CurrentSceneCollectionChanged', async (data) => {
    console.log('CurrentSceneCollectionChanged', data.sceneCollectionName)
    currentCollectionD = data.sceneCollectionName || ''
    // Manually emit new scenes, since OBS doesn't send them when the collection changes
    obsD.emit('SceneListChanged', await sendCommandD('GetSceneList'))
  })
  obsD.on('SceneCollectionListChanged', async (data) => {
    console.log('SceneCollectionListChanged', data.sceneCollections.length)
    collectionsD = data.sceneCollections || []
  })

  async function setCurrentCollection (event) {
    sendCommandD('SetCurrentSceneCollection', { sceneCollectionName: event.target.value })
  }
</script>

<!-- <div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentCollectionD} title="Change Collection" on:change={setCurrentCollection}>
  {#each collectionsD as collection}
    <option value={collection}>{collection}</option>
  {/each}
  </select>
</div> -->

<button class="button is-info is-dark" disabled style="margin: 0 .5rem .5rem 0;">
  <span>{currentCollectionD}</span>
</button>
