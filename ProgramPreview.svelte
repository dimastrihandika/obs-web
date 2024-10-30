<script>
  import { onMount, onDestroy } from 'svelte'
  import { obs, sendCommand } from './obs.js'

  export let imageFormat = 'jpg'
  const microphoneSourceName = 'Audio Input Capture'; // Ganti dengan nama sumber mikrofon Anda

  let isStudioMode = false
  let programScene = ''
  let previewScene = ''
  let microphoneLevel = 0

  let program = {}
  let preview = {}
  let screenshotInterval
  let transitions = []

  onMount(async () => {
    let data;

    // Mendapatkan daftar input
    data = await sendCommand('GetInputList');
    console.log('Available Inputs:', data);

    // Memastikan mikrofon ada dalam daftar input
    const microphone = data.inputs.find(input => input.inputName === microphoneSourceName);
    if (!microphone) {
      console.error(`Microphone source "${microphoneSourceName}" not found.`);
      return;
    }

    // Mendapatkan scene program saat ini
    if (!programScene) {
      data = await sendCommand('GetCurrentProgramScene');
      programScene = data.currentProgramSceneName || '';
    }

    // Memeriksa apakah Studio Mode aktif
    data = await sendCommand('GetStudioModeEnabled');
    isStudioMode = data.studioModeEnabled || false;

    // Mendapatkan scene preview jika Studio Mode aktif
    if (isStudioMode) {
      data = await sendCommand('GetCurrentPreviewScene');
      previewScene = data.currentPreviewSceneName || '';
    }

    // Mendapatkan daftar transisi
    data = await sendCommand('GetSceneTransitionList');
    transitions = data.transitions || [];

    // Mengambil screenshot dan memperbarui level audio setiap detik
    screenshotInterval = setInterval(() => {
      getScreenshot();
      updateAudioLevel(); // Memperbarui level audio
    }, 1000);
  });

  onDestroy(() => {
    clearInterval(screenshotInterval);
  });

  // Fungsi untuk memperbarui level audio
  async function updateAudioLevel() {
    try {
      const data = await sendCommand('GetInputVolume', { inputName: microphoneSourceName });
      microphoneLevel = data.inputVolume; // Dapatkan level volume mikrofon
    } catch (error) {
      console.error('Error getting audio level:', error);
    }
  }

  async function getScreenshot() {
    if (!programScene) return
    let data = await sendCommand('GetSourceScreenshot', {
      sourceName: programScene,
      imageFormat,
      imageWidth: 854,
      imageHeight: 480
    })
    if (data && data.imageData && program) {
      program.src = data.imageData
      program.className = ''
    }

    if (isStudioMode) {
      if (previewScene !== programScene) {
        data = await sendCommand('GetSourceScreenshot', {
          sourceName: previewScene,
          imageFormat,
          imageWidth: 854,
          imageHeight: 480
        })
      }
      if (data && data.imageData && preview) {
        preview.src = data.imageData
      }
    }
  }

  // Menangani peristiwa dari OBS
  obs.on('StudioModeStateChanged', async (data) => {
    isStudioMode = data.studioModeEnabled;
    if (isStudioMode) {
      previewScene = programScene;
    }
  });

  obs.on('CurrentPreviewSceneChanged', async (data) => {
    previewScene = data.sceneName;
  });

  obs.on('CurrentProgramSceneChanged', async (data) => {
    programScene = data.sceneName;
  });

  obs.on('SceneNameChanged', async (data) => {
    if (data.oldSceneName === programScene) programScene = data.sceneName;
    if (data.oldSceneName === previewScene) previewScene = data.sceneName;
  });

  obs.on('TransitionListChanged', async (data) => {
    transitions = data.transitions || [];
  });
</script>

<div class="columns is-centered is-vcentered has-text-centered">
  {#if isStudioMode}
    <div class="column">
      <img bind:this={preview} class="has-background-dark" alt="Preview" />
    </div>
    <div class="column is-narrow">
      {#each transitions as transition}
      <button class="button is-fullwidth is-info" style="margin-bottom: .5rem;"
        on:click={async () => {
          await sendCommand('SetCurrentSceneTransition', { transitionName: transition.transitionName });
          await sendCommand('TriggerStudioModeTransition');
        }}
        >{transition.transitionName}</button>
      {/each}
    </div>
  {/if}
  <div class="column">
    <img bind:this={program} alt="Program"/>
  </div>
</div>

<!-- Audio Level Bar -->
<div class="audio-level-bar" style="width: 100%; background-color: #e0e0e0; border-radius: 5px;">
  <div class="audio-level" style="width: {microphoneLevel * 100}%; height: 20px; background-color: #76c7c0; border-radius: 5px;"></div>
</div>
