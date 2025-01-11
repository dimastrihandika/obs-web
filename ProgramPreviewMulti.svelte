<script>
  import { onMount, onDestroy } from 'svelte'
  import { obs, obsB, sendCommand, sendCommandB } from './obs.js'


  let isStudioMode = false
  let programScene = ''
  let previewScene = ''
  let transitions = []

  let isStudioModeB = false
  let programSceneB = ''
  let previewSceneB = ''
  let transitionsB = []
  // let currentTransition = ''
  // let currentTransition = ''

  onMount(async () => {
    let data
    if (!programScene) {
      data = await sendCommand('GetCurrentProgramScene')
      programScene = data.currentProgramSceneName || ''
    }
    data = await sendCommand('GetStudioModeEnabled')
    if (data && data.studioModeEnabled) {
      isStudioMode = true
      data = await sendCommand('GetCurrentPreviewScene')
      previewScene = data.currentPreviewSceneName || ''
    }

    data = await sendCommand('GetSceneTransitionList')
    console.log('GetSceneTransitionList', data)
    transitions = data.transitions || []
    
  })

  onMount(async () => {
    let dataB
    if (!programSceneB) {
      dataB = await sendCommandB('GetCurrentProgramScene')
      programSceneB = dataB.currentProgramSceneName || ''
    }
    dataB = await sendCommandB('GetStudioModeEnabled')
    if (dataB && dataB.studioModeEnabled) {
      isStudioModeB = true
      dataB = await sendCommandB('GetCurrentPreviewScene')
      previewSceneB = dataB.currentPreviewSceneName || ''
    }

    dataB = await sendCommandB('GetSceneTransitionList')
    console.log('GetSceneTransitionList', dataB)
    transitions = dataB.transitions || []
    
  })

  
</script>

<div class="sections">
  {#if isStudioMode && isStudioModeB}
    <div class="sections">
      {#each transitions as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommand('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommand('TriggerStudioModeTransition')
          await sendCommandB('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandB('TriggerStudioModeTransition')
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
</div>
