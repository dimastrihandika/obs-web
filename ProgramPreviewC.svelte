<script>
  import { onMount, onDestroy } from 'svelte'
  import { obsC, sendCommandC } from './obs.js'

  export let imageFormat = 'jpg'

  let isStudioModeC = false
  let programSceneC = ''
  let previewSceneC = ''

  let programC = {}
  let previewC = {}
  let screenshotInterval
  let transitionsC = []
  // let currentTransition = ''

  onMount(async () => {
    let data
    if (!programSceneC) {
      data = await sendCommandC('GetCurrentProgramScene')
      programSceneC = data.currentProgramSceneName || ''
    }
    data = await sendCommandC('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioModeC = true
      data = await sendCommandC('GetCurrentPreviewScene')
      previewSceneC = data.currentPreviewSceneName || ''
    }

    data = await sendCommandC('GetSceneTransitionList')
    console.log('GetSceneTransitionList', data)
    transitionsC = data.transitions || []
    // currentTransition = data.currentSceneTransitionName || ''
    screenshotInterval = setInterval(getScreenshotB, 125)
  })

  onDestroy(() => {
    clearInterval(screenshotInterval)
  })

  // eslint-disable-next-line
  $: getScreenshotB(), programSceneC, previewSceneC

  obsC.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeC = data.studioModeEnabled
    if (isStudioModeC) {
      previewSceneC = programSceneC
    }B
  })

  obsC.on('CurrentPreviewSceneChanged', async (data) => {
    console.log('CurrentPreviewSceneChanged', data.sceneName)
    previewSceneC = data.sceneName
  })

  obsC.on('CurrentProgramSceneChanged', async (data) => {
    console.log('CurrentProgramSceneChanged', data.sceneName)
    programSceneC = data.sceneName
  })

  obsC.on('SceneNameChanged', async (data) => {
    if (data.oldSceneName === programSceneC) programSceneC = data.sceneName
    if (data.oldSceneName === previewSceneC) previewSceneC = data.sceneName
  })

  // TODO: does not exist???
  obsC.on('TransitionListChanged', async (data) => {
    console.log('TransitionListChanged', data)
    transitionsC = data.transitions || []
  })

  async function getScreenshotB () {
    if (!programSceneC) return
    let data = await sendCommandC('GetSourceScreenshot', {
      sourceName: programSceneC,
      imageFormat,
      imageWidth: 960,
      imageHeight: 540
    })
    if (data && data.imageData && programC) {
      programC.src = data.imageData
      programC.className = ''
    }

    if (isStudioModeC) {
      if (previewSceneC !== programSceneC) {
        data = await sendCommandC('GetSourceScreenshot', {
          sourceName: previewSceneC,
          imageFormat,
          imageWidth: 960,
          imageHeight: 540
        })
      }
      if (data && data.imageData && previewC) {
        previewC.src = data.imageData
      }
    }
  }
</script>

<div class="columns is-centered is-vcentered has-text-centered">
  {#if isStudioModeC}
    <div class="column">
      <img bind:this={previewC} class="has-background-dark" alt="PreviewC" />
    </div>
    <div class="column is-narrow">
      {#each transitionsC as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommandC('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandC('TriggerStudioModeTransition')
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
  <div class="column">
    <img bind:this={programC} alt="ProgramC"/>
  </div>
</div>
