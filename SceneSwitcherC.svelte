<script>
  import { onMount } from 'svelte'
  import { obsC, sendCommandC } from './obs.js'
  import SourceButton from './SourceButton.svelte'

  export let programSceneC = {}
  export let previewSceneC = {}
  export let scenesC = []
  export let buttonStyle = 'text' // text, screenshot, icon
  export let editableC = false

  let scenesFilteredC = []
  let isStudioModeC = false
  const sceneIcons = JSON.parse(window.localStorage.getItem('sceneIcons') || '{}')

  $: scenesFilteredC = scenesC.filter((scene) => scene.sceneName.indexOf('(hidden)') === -1).reverse()
  // store sceneIcons on change
  $: window.localStorage.setItem('sceneIcons', JSON.stringify(sceneIcons))

  onMount(async function () {
    let data = await sendCommandC('GetSceneList')
    console.log('GetSceneList', data)
    programSceneC = data.currentProgramSceneName || ''
    previewSceneC = data.currentPreviewSceneName
    scenesC = data.scenes
    data = await sendCommandC('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioModeC = true
      previewSceneC = data.currentPreviewSceneName || ''
    }
  })

  obsC.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeC = data.studioModeEnabled
    previewSceneC = programSceneC
  })

  obsC.on('SceneListChanged', async (data) => {
    console.log('SceneListChanged', data.scenes.length)
    scenesC = data.scenes
  })

  obsC.on('SceneCreated', async (data) => {
    console.log('SceneCreated', data)
  })

  obsC.on('SceneRemoved', async (data) => {
    console.log('SceneRemoved', data)
    for (let i = 0; i < scenesC.length; i++) {
      if (scenesC[i].sceneName === data.sceneName) {
        delete scenesC[i]
      }
    }
  })

  obsC.on('SceneNameChanged', async (data) => {
    console.log('SceneNameChanged', data)
    for (let i = 0; i < scenesC.length; i++) {
      if (scenesC[i].sceneName === data.oldSceneName) {
        scenesC[i].sceneName = data.sceneName
      }
    }
    // Rename in sceneIcons
    sceneIcons[data.sceneName] = sceneIcons[data.oldSceneName]
  })

  obsC.on('CurrentProgramSceneChanged', (data) => {
    console.log('CurrentProgramSceneChanged', data)
    programSceneC = data.sceneName || ''
  })

  obsC.on('CurrentPreviewSceneChanged', async (data) => {
    console.log('CurrentPreviewSceneChanged', data)
    previewSceneC = data.sceneName
  })

  function sceneClicker (scene) {
    return async function () {
      if (isStudioModeC) {
        await sendCommandC('SetCurrentPreviewScene', { sceneName: scene.sceneName })
      } else {
        await sendCommandC('SetCurrentProgramScene', { sceneName: scene.sceneName })
      }
    }
  }

  function onNameChange (event) {
    sendCommandC('SetSceneName', { sceneName: event.target.title, newSceneName: event.target.value })
  }
  function onIconChange (event) {
    sceneIcons[event.target.title] = event.target.value
  }
</script>

<ol
  class:column={editableC}
  class:with-icon={buttonStyle === 'icon'}
  >
  {#if editableC}
    {#each scenesC.reverse() as scene}
    <li>
      <!-- svelte-ignore a11y-label-has-associated-control -->
      <label class="label">Name</label>
      <input type="text" class="input" title={scene.sceneName} value={scene.sceneName} on:change={onNameChange} />
      <!-- svelte-ignore a11y-label-has-associated-control -->
      <label class="label">Icon</label>
      <input type="text" class="input" title={scene.sceneName} value={sceneIcons[scene.sceneName] || ''} on:change={onIconChange} />
    </li>
    {/each}
  {:else}
    {#each scenesFilteredC as scene}
    <li>
      <SourceButton name={scene.sceneName}
        on:click={sceneClicker(scene)}
        isProgram={programSceneC === scene.sceneName}
        isPreview={previewSceneC === scene.sceneName}
        buttonStyle={buttonStyle}
        icon={sceneIcons[scene.sceneName] || `#${Math.floor(Math.random() * 16777215).toString(16)}`}
      />
    </li>
    {/each}
  {/if}
</ol>

<style>
  ol {
    list-style: None;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: .5rem;
    margin-bottom: 10px;
  }
  ol.column {
    flex-direction: column;
    gap: 1rem;
  }
  li {
    display: inline-block;
    min-width: 10rem;
    flex-grow: 1;
  }
  ol.with-icon {
    justify-content: center;
  }
  ol.with-icon li {
    min-width: 0;
    flex-grow: 0;
    flex-shrink: 1;
  }
</style>
