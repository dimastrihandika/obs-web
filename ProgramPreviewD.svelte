<script>
  import { onMount, onDestroy } from 'svelte'
  import { obsD, sendCommandD } from './obs.js'

  export let imageFormat = 'jpg'

  let isStudioModeD = false
  let programSceneD = ''
  let previewSceneD = ''

  let programD = {}
  let previewD = {}
  let screenshotInterval
  let transitionsD = []
  // let currentTransition = ''

  onMount(async () => {
    let data
    if (!programSceneD) {
      data = await sendCommandD('GetCurrentProgramScene')
      programSceneD = data.currentProgramSceneName || ''
    }
    data = await sendCommandD('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioModeD = true
      data = await sendCommandD('GetCurrentPreviewScene')
      previewSceneD = data.currentPreviewSceneName || ''
    }

    data = await sendCommandD('GetSceneTransitionList')
    console.log('GetSceneTransitionList', data)
    transitionsD = data.transitions || []
    // currentTransition = data.currentSceneTransitionName || ''
    screenshotInterval = setInterval(getScreenshotB, 125)
  })

  onDestroy(() => {
    clearInterval(screenshotInterval)
  })

  // eslint-disable-next-line
  $: getScreenshotB(), programSceneD, previewSceneD

  obsD.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeD = data.studioModeEnabled
    if (isStudioModeD) {
      previewSceneD = programSceneD
    }B
  })

  obsD.on('CurrentPreviewSceneChanged', async (data) => {
    console.log('CurrentPreviewSceneChanged', data.sceneName)
    previewSceneD = data.sceneName
  })

  obsD.on('CurrentProgramSceneChanged', async (data) => {
    console.log('CurrentProgramSceneChanged', data.sceneName)
    programSceneD = data.sceneName
  })

  obsD.on('SceneNameChanged', async (data) => {
    if (data.oldSceneName === programSceneD) programSceneD = data.sceneName
    if (data.oldSceneName === previewSceneD) previewSceneD = data.sceneName
  })

  // TODO: does not exist???
  obsD.on('TransitionListChanged', async (data) => {
    console.log('TransitionListChanged', data)
    transitionsD = data.transitions || []
  })

  async function getScreenshotB () {
    if (!programSceneD) return
    let data = await sendCommandD('GetSourceScreenshot', {
      sourceName: programSceneD,
      imageFormat,
      imageWidth: 960,
      imageHeight: 540
    })
    if (data && data.imageData && programD) {
      programD.src = data.imageData
      programD.className = ''
    }

    if (isStudioModeD) {
      if (previewSceneD !== programSceneD) {
        data = await sendCommandD('GetSourceScreenshot', {
          sourceName: previewSceneD,
          imageFormat,
          imageWidth: 960,
          imageHeight: 540
        })
      }
      if (data && data.imageData && previewD) {
        previewD.src = data.imageData
      }
    }
  }
</script>

<div class="columns is-centered is-vcentered has-text-centered">
  {#if isStudioModeD}
    <div class="column">
      <img bind:this={previewD} class="has-background-dark" alt="PreviewD" />
    </div>
    <div class="column is-narrow">
      {#each transitionsD as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommandD('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandD('TriggerStudioModeTransition')
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
  <div class="column">
    <img bind:this={programD} alt="ProgramD"/>
  </div>
</div>
