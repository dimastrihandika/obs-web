<script>
  import { onMount, onDestroy } from 'svelte'
  import { obsC, obsD, sendCommandC, sendCommandD } from './obs.js'


  let isStudioModeC = false
  let programSceneC = ''
  let previewSceneC = ''
  let transitions = []

  let isStudioModeD = false
  let programSceneD = ''
  let previewSceneD = ''
  let transitionsD = []
  // let currentTransition = ''
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
  {#if isStudioModeC && isStudioModeD}
    <div class="sections">
      {#each transitions as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommandC('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandC('TriggerStudioModeTransition')
          await sendCommandD('SetCurrentSceneTransition', { transitionName: transition.transitionName })
          await sendCommandD('TriggerStudioModeTransition')
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
</div>
