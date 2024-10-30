<script>
  import { onMount } from 'svelte'
  import { obsB, sendCommandB } from './obs.js'
  import SourceButton from './SourceButton.svelte'

  export let programSceneB = {}
  export let previewSceneB = {}
  export let scenesB = []
  export let buttonStyle = 'text' // text, screenshot, icon
  export let editableB = false

  let scenesFilteredB = []
  let isStudioModeB = false
  const sceneIcons = JSON.parse(window.localStorage.getItem('sceneIcons') || '{}')

  $: scenesFilteredB = scenesB.filter((scene) => scene.sceneName.indexOf('(hidden)') === -1).reverse()
  // store sceneIcons on change
  $: window.localStorage.setItem('sceneIcons', JSON.stringify(sceneIcons))

  onMount(async function () {
    let data = await sendCommandB('GetSceneList')
    console.log('GetSceneList', data)
    programSceneB = data.currentProgramSceneName || ''
    previewSceneB = data.currentPreviewSceneName
    scenesB = data.scenes
    data = await sendCommandB('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioModeB = true
      previewSceneB = data.currentPreviewSceneName || ''
    }
  })

  obsB.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeB = data.studioModeEnabled
    previewSceneB = programSceneB
  })

  obsB.on('SceneListChanged', async (data) => {
    console.log('SceneListChanged', data.scenes.length)
    scenesB = data.scenes
  })

  obsB.on('SceneCreated', async (data) => {
    console.log('SceneCreated', data)
  })

  obsB.on('SceneRemoved', async (data) => {
    console.log('SceneRemoved', data)
    for (let i = 0; i < scenesB.length; i++) {
      if (scenesB[i].sceneName === data.sceneName) {
        delete scenesB[i]
      }
    }
  })

  obsB.on('SceneNameChanged', async (data) => {
    console.log('SceneNameChanged', data)
    for (let i = 0; i < scenesB.length; i++) {
      if (scenesB[i].sceneName === data.oldSceneName) {
        scenesB[i].sceneName = data.sceneName
      }
    }
    // Rename in sceneIcons
    sceneIcons[data.sceneName] = sceneIcons[data.oldSceneName]
  })

  obsB.on('CurrentProgramSceneChanged', (data) => {
    console.log('CurrentProgramSceneChanged', data)
    programSceneB = data.sceneName || ''
  })

  obsB.on('CurrentPreviewSceneChanged', async (data) => {
    console.log('CurrentPreviewSceneChanged', data)
    previewSceneB = data.sceneName
  })

  function sceneClicker (scene) {
    return async function () {
      if (isStudioModeB) {
        await sendCommandB('SetCurrentPreviewScene', { sceneName: scene.sceneName })
      } else {
        await sendCommandB('SetCurrentProgramScene', { sceneName: scene.sceneName })
      }
    }
  }

  function onNameChange (event) {
    sendCommandB('SetSceneName', { sceneName: event.target.title, newSceneName: event.target.value })
  }
  function onIconChange (event) {
    sceneIcons[event.target.title] = event.target.value
  }
</script>

<ol
  class:column={editableB}
  class:with-icon={buttonStyle === 'icon'}
  >
  {#if editableB}
    {#each scenesB.reverse() as scene}
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
    {#each scenesFilteredB as scene}
    <li>
      <SourceButton name={scene.sceneName}
        on:click={sceneClicker(scene)}
        isProgram={programSceneB === scene.sceneName}
        isPreview={previewSceneB === scene.sceneName}
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
    margin-bottom: 2rem;
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
