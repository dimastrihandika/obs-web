<script>
  import { onMount } from 'svelte'
  import { obsD, sendCommandD } from './obs.js'
  import SourceButton from './SourceButton.svelte'

  export let programSceneD = {}
  export let previewSceneD = {}
  export let scenesD = []
  export let buttonStyle = 'text' // text, screenshot, icon
  export let editableD = false

  let scenesFilteredD = []
  let isStudioModeD = false
  const sceneIcons = JSON.parse(window.localStorage.getItem('sceneIcons') || '{}')

  $: scenesFilteredD = scenesD.filter((scene) => scene.sceneName.indexOf('(hidden)') === -1).reverse()
  // store sceneIcons on change
  $: window.localStorage.setItem('sceneIcons', JSON.stringify(sceneIcons))

  onMount(async function () {
    let data = await sendCommandD('GetSceneList')
    console.log('GetSceneList', data)
    programSceneD = data.currentProgramSceneName || ''
    previewSceneD = data.currentPreviewSceneName
    scenesD = data.scenes
    data = await sendCommandD('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioModeD = true
      previewSceneD = data.currentPreviewSceneName || ''
    }
  })

  obsD.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeD = data.studioModeEnabled
    previewSceneD = programSceneD
  })

  obsD.on('SceneListChanged', async (data) => {
    console.log('SceneListChanged', data.scenes.length)
    scenesD = data.scenes
  })

  obsD.on('SceneCreated', async (data) => {
    console.log('SceneCreated', data)
  })

  obsD.on('SceneRemoved', async (data) => {
    console.log('SceneRemoved', data)
    for (let i = 0; i < scenesD.length; i++) {
      if (scenesD[i].sceneName === data.sceneName) {
        delete scenesD[i]
      }
    }
  })

  obsD.on('SceneNameChanged', async (data) => {
    console.log('SceneNameChanged', data)
    for (let i = 0; i < scenesD.length; i++) {
      if (scenesD[i].sceneName === data.oldSceneName) {
        scenesD[i].sceneName = data.sceneName
      }
    }
    // Rename in sceneIcons
    sceneIcons[data.sceneName] = sceneIcons[data.oldSceneName]
  })

  obsD.on('CurrentProgramSceneChanged', (data) => {
    console.log('CurrentProgramSceneChanged', data)
    programSceneD = data.sceneName || ''
  })

  obsD.on('CurrentPreviewSceneChanged', async (data) => {
    console.log('CurrentPreviewSceneChanged', data)
    previewSceneD = data.sceneName
  })

  function sceneClicker (scene) {
    return async function () {
      if (isStudioModeD) {
        await sendCommandD('SetCurrentPreviewScene', { sceneName: scene.sceneName })
      } else {
        await sendCommandD('SetCurrentProgramScene', { sceneName: scene.sceneName })
      }
    }
  }

  function onNameChange (event) {
    sendCommandD('SetSceneName', { sceneName: event.target.title, newSceneName: event.target.value })
  }
  function onIconChange (event) {
    sceneIcons[event.target.title] = event.target.value
  }
</script>

<ol
  class:column={editableD}
  class:with-icon={buttonStyle === 'icon'}
  >
  {#if editableD}
    {#each scenesD.reverse() as scene}
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
    {#each scenesFilteredD as scene}
    <li>
      <SourceButton name={scene.sceneName}
        on:click={sceneClicker(scene)}
        isProgram={programSceneD === scene.sceneName}
        isPreview={previewSceneD === scene.sceneName}
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
