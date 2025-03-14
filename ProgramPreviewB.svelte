<script>
  import { onMount, onDestroy } from 'svelte'
  import { obsB, sendCommandB } from './obs.js'

  export let imageFormat = 'jpg'

  let isStudioModeB = false
  let programSceneB = ''
  let previewSceneB = ''

  let programB = {}
  let previewB = {}
  let screenshotInterval
  let transitionsB = []
  // let currentTransition = ''

  onMount(async () => {
    let data
    if (!programSceneB) {
      data = await sendCommandB('GetCurrentProgramScene')
      programSceneB = data.currentProgramSceneName || ''
    }
    data = await sendCommandB('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioModeB = true
      data = await sendCommandB('GetCurrentPreviewScene')
      previewSceneB = data.currentPreviewSceneName || ''
    }

    data = await sendCommandB('GetSceneTransitionList')
    console.log('GetSceneTransitionList', data)
    transitionsB = data.transitions || []
    // currentTransition = data.currentSceneTransitionName || ''
    screenshotInterval = setInterval(getScreenshotB, 125)
  })

  onDestroy(() => {
    clearInterval(screenshotInterval)
  })

  // eslint-disable-next-line
  $: getScreenshotB(), programSceneB, previewSceneB

  obsB.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeB = data.studioModeEnabled
    if (isStudioModeB) {
      previewSceneB = programSceneB
    }B
  })

  obsB.on('CurrentPreviewSceneChanged', async (data) => {
    console.log('CurrentPreviewSceneChanged', data.sceneName)
    previewSceneB = data.sceneName
  })

  obsB.on('CurrentProgramSceneChanged', async (data) => {
    console.log('CurrentProgramSceneChanged', data.sceneName)
    programSceneB = data.sceneName
  })

  obsB.on('SceneNameChanged', async (data) => {
    if (data.oldSceneName === programSceneB) programSceneB = data.sceneName
    if (data.oldSceneName === previewSceneB) previewSceneB = data.sceneName
  })

  // TODO: does not exist???
  obsB.on('TransitionListChanged', async (data) => {
    console.log('TransitionListChanged', data)
    transitionsB = data.transitions || []
  })

  async function getScreenshotB () {
    if (!programSceneB) return
    let data = await sendCommandB('GetSourceScreenshot', {
      sourceName: programSceneB,
      imageFormat,
      imageWidth: 960,
      imageHeight: 540
    })
    if (data && data.imageData && programB) {
      programB.src = data.imageData
      programB.className = ''
    }

    if (isStudioModeB) {
      if (previewSceneB !== programSceneB) {
        data = await sendCommandB('GetSourceScreenshot', {
          sourceName: previewSceneB,
          imageFormat,
          imageWidth: 960,
          imageHeight: 540
        })
      }
      if (data && data.imageData && previewB) {
        previewB.src = data.imageData
      }
    }
  }
</script>

<div class="columns is-centered is-vcentered has-text-centered">
  {#if isStudioModeB}
    <div class="column">
      <img bind:this={previewB} class="has-background-dark" alt="PreviewB" />
    </div>
    <div class="column is-narrow">
      {#each transitionsB as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommandB('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandB('TriggerStudioModeTransition')
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
  <div class="column">
    <img bind:this={programB} alt="ProgramB"/>
  </div>
</div>
