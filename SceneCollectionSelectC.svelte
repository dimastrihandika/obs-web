<script>
  import { onMount } from 'svelte'
  import { obsC, sendCommandC } from './obs.js'

  let collectionsC = []
  let currentCollectionC = ''

  onMount(async function () {
    const data = await sendCommandC('GetSceneCollectionList')
    collectionsC = data.sceneCollections || []
    currentCollectionC = data.currentSceneCollectionName || ''
  })

  obsC.on('CurrentSceneCollectionChanged', async (data) => {
    console.log('CurrentSceneCollectionChanged', data.sceneCollectionName)
    currentCollectionC = data.sceneCollectionName || ''
    // Manually emit new scenes, since OBS doesn't send them when the collection changes
    obsC.emit('SceneListChanged', await sendCommandC('GetSceneList'))
  })
  obsC.on('SceneCollectionListChanged', async (data) => {
    console.log('SceneCollectionListChanged', data.sceneCollections.length)
    collectionsC = data.sceneCollections || []
  })

  async function setCurrentCollection (event) {
    sendCommandC('SetCurrentSceneCollection', { sceneCollectionName: event.target.value })
  }
</script>

<!-- <div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentCollectionC} title="Change Collection" on:change={setCurrentCollection}>
  {#each collectionsC as collection}
    <option value={collection}>{collection}</option>
  {/each}
  </select>
</div> -->

<button class="button is-info is-dark" disabled style="margin: 0 .5rem .5rem 0;">
  <span>{currentCollectionC}</span>
</button>
