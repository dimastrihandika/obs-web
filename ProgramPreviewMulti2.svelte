<script>
  import { onMount, onDestroy } from 'svelte'
  import { obs, obsB, sendCommand, sendCommandD } from './obs.js'


  let isStudioMode = false
  let programScene = ''
  let previewScene = ''
  let transitions = []

  let isStudioModeD = false
  let programSceneD = ''
  let previewSceneD = ''
  let transitionsD = []
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
    let dataD
    if (!programSceneD) {
      dataD = await sendCommandD('GetCurrentProgramScene')
      programSceneD = dataD.currentProgramSceneName || ''
    }
    dataD = await sendCommandD('GetStudioModeEnabled')
    if (dataD && dataD.studioModeEnabled) {
      isStudioModeD = true
      dataD = await sendCommandD('GetCurrentPreviewScene')
      previewSceneD = dataD.currentPreviewSceneName || ''
    }

    dataD = await sendCommandD('GetSceneTransitionList')
    console.log('GetSceneTransitionList', dataD)
    transitions = dataD.transitions || []
    
  })

  
</script>

<div class="sections">
  {#if isStudioMode && isStudioModeD}
    <div class="sections">
      {#each transitions as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommand('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommand('TriggerStudioModeTransition')
          await sendCommandD('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandD('TriggerStudioModeTransition')
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
</div>
