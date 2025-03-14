<script>
  import { onMount } from 'svelte'
  import { obsB, sendCommandB } from './obs.js'

  let collectionsB = []
  let currentCollectionB = ''

  onMount(async function () {
    const data = await sendCommandB('GetSceneCollectionList')
    collectionsB = data.sceneCollections || []
    currentCollectionB = data.currentSceneCollectionName || ''
  })

  obsB.on('CurrentSceneCollectionChanged', async (data) => {
    console.log('CurrentSceneCollectionChanged', data.sceneCollectionName)
    currentCollectionB = data.sceneCollectionName || ''
    // Manually emit new scenes, since OBS doesn't send them when the collection changes
    obsB.emit('SceneListChanged', await sendCommandB('GetSceneList'))
  })
  obsB.on('SceneCollectionListChanged', async (data) => {
    console.log('SceneCollectionListChanged', data.sceneCollections.length)
    collectionsB = data.sceneCollections || []
  })

  async function setCurrentCollection (event) {
    sendCommandB('SetCurrentSceneCollection', { sceneCollectionName: event.target.value })
  }
</script>

<!-- <div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentCollectionB} title="Change Collection" on:change={setCurrentCollection}>
  {#each collectionsB as collection}
    <option value={collection}>{collection}</option>
  {/each}
  </select>
</div> -->

<button class="button is-info is-dark" disabled style="margin: 0 .5rem .5rem 0;">
  <span>{currentCollectionB}</span>
</button>
