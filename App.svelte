<script>
  /* eslint-env browser */
  const OBS_WEBSOCKET_LATEST_VERSION = '5.0.1' // https://api.github.com/repos/Palakis/obs-websocket/releases/latest

  // Imports
  import { onMount } from 'svelte'
  // State untuk menyimpan daftar koneksi OBS dan koneksi yang dipilih.
  let connections = [
    { category: 'primary', name: 'WG 100 - Livoli (P)', address: 'ws://192.168.1.6:4444', hidden: true},
    { category: 'primary', name: 'WG 100 -  Liga 1 Arema vs Persija (P)', address: 'ws://192.168.1.2:4444', hidden: false},
    { category: 'backup', name: 'WG 100 -  Liga 1 Arema vs Persija (Bakul)', address: 'ws://192.168.1.2:4445', hidden: false},
    { category: 'backup', name: 'OBS Server 4', address: 'ws://192.168.40.65:4445', hidden: false},
    { category: 'tv', name: 'OBS Server 5', address: 'ws://192.168.110.65:4444', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 6', address: 'ws://192.168.110.65:4444', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 16', address: 'ws://192.168.110.65:4449', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 26', address: 'ws://192.168.110.65:4445', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 36', address: 'ws://192.168.110.65:4447', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 46', address: 'ws://192.168.110.65:4448', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 646', address: 'ws://192.168.110.65:4441', password: 'mypasswordss' },
    { category: 'tv-backup', name: 'OBS Server 546', address: 'ws://192.168.110.65:4440', password: 'mypasswordss' },
  ];
  
  // GET ITEM FROM LOCAL STORAGE
// let selectedConnection = connections[localStorage.getItem('selectedConnection') || 0];
// let selectedConnectionB = connections[localStorage.getItem('selectedConnection') || 1];

  let selectedConnection = connections[0]; // Default connection
  let selectedConnectionB = connections[1];

  let connectionStatus = 'Disconnected';
  let connectionStatusB = 'Disconnected';

  let { address, password } = selectedConnection;

  import {
    mdiSquareRoundedBadge,
    mdiSquareRoundedBadgeOutline,
    mdiImageEdit,
    mdiImageEditOutline,
    mdiFullscreen,
    mdiFullscreenExit,
    mdiBorderVertical,
    mdiArrowSplitHorizontal,
    mdiAccessPoint,
    mdiAccessPointOff,
    mdiRecord,
    mdiStop,
    mdiPause,
    mdiPlayPause,
    mdiConnection,
    mdiCameraOff,
    mdiCamera,
    mdiMotionPlayOutline,
    mdiMotionPlay
  } from '@mdi/js'
  import Icon from 'mdi-svelte'
  import { compareVersions } from 'compare-versions'

  import './style.scss'
  import { obs, sendCommand, obsB, sendCommandB } from './obs.js'
  import ProgramPreview from './ProgramPreview.svelte'
  import ProgramPreviewMulti from './ProgramPreviewMulti.svelte'
  import SceneSwitcher from './SceneSwitcher.svelte'
  import SourceSwitcher from './SourceSwitcher.svelte'
  import ProfileSelect from './ProfileSelect.svelte'
  import SceneCollectionSelect from './SceneCollectionSelect.svelte'
  import ProgramPreviewB from './ProgramPreviewB.svelte'
  import SceneSwitcherB from './SceneSwitcherB.svelte'
  import SourceSwitcherB from './SourceSwitcherB.svelte'
  import ProfileSelectB from './ProfileSelectB.svelte'
  import SceneCollectionSelectB from './SceneCollectionSelectB.svelte'

  onMount(async () => {
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('/service-worker.js')
    }

    // Request screen wakelock
    if ('wakeLock' in navigator) {
      try {
        await navigator.wakeLock.request('screen')
        // Re-request when coming back
        document.addEventListener('visibilitychange', async () => {
          if (document.visibilityState === 'visible') {
            await navigator.wakeLock.request('screen')
          }
        })
      } catch (e) {}
    }

    // Toggle the navigation hamburger menu on mobile
    const navbar = document.querySelector('.navbar-burger')
    navbar.addEventListener('click', () => {
      navbar.classList.toggle('is-active')
      document
        .getElementById(navbar.dataset.target)
        .classList.toggle('is-active')
    })

    // Listen for fullscreen changes
    document.addEventListener('fullscreenchange', () => {
      isFullScreen = document.fullscreenElement
    })

    document.addEventListener('webkitfullscreenchange', () => {
      isFullScreen = document.webkitFullscreenElement
    })

    document.addEventListener('msfullscreenchange', () => {
      isFullScreen = document.msFullscreenElement
    })

    if (document.location.hash !== '') {
      // Read address from hash
      address = document.location.hash.slice(1)

      // This allows you to add a password in the URL like this:
      // http://obs-web.niek.tv/#ws://localhost:4455#password
      if (address.includes('#')) {
        [address, password] = address.split('#')
      }
      await connect()
    }

    // Export the sendCommand() function to the window objects
    window.sendCommand = sendCommand
  })

  onMount(async () => {
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('/service-worker.js')
    }

    // Request screen wakelock
    if ('wakeLock' in navigator) {
      try {
        await navigator.wakeLock.request('screen')
        // Re-request when coming back
        document.addEventListener('visibilitychange', async () => {
          if (document.visibilityState === 'visible') {
            await navigator.wakeLock.request('screen')
          }
        })
      } catch (e) {}
    }

    // Toggle the navigation hamburger menu on mobile
    const navbar = document.querySelector('.navbar-burger')
    navbar.addEventListener('click', () => {
      navbar.classList.toggle('is-active')
      document
        .getElementById(navbar.dataset.target)
        .classList.toggle('is-active')
    })

    // Listen for fullscreen changes
    document.addEventListener('fullscreenchange', () => {
      isFullScreenB = document.fullscreenElement
    })

    document.addEventListener('webkitfullscreenchange', () => {
      isFullScreenB = document.webkitFullscreenElement
    })

    document.addEventListener('msfullscreenchange', () => {
      isFullScreenB = document.msFullscreenElement
    })

    if (document.location.hash !== '') {
      // Read address from hash
      address = document.location.hash.slice(1)

      // This allows you to add a password in the URL like this:
      // http://obs-web.niek.tv/#ws://localhost:4455#password
      if (address.includes('#')) {
        [address, password] = address.split('#')
      }
      await connectB()
    }

    // Export the sendCommand() function to the window objects
    window.sendCommand = sendCommandB
  })

  

  // State
  let connected
  let heartbeat = {}
  let heartbeatInterval
  let isFullScreen
  let isStudioMode
  let isSceneOnTop = window.localStorage.getItem('isSceneOnTop') || false
  let isVirtualCamActive
  let isIconMode = window.localStorage.getItem('isIconMode') || false
  let isReplaying
  let editable = false
  let scenes = []
  let replayError = ''
  let errorMessage = ''
  let imageFormat = 'jpg'

  let connectedB
  let heartbeatB = {}
  let heartbeatIntervalB
  let isFullScreenB
  let isStudioModeB
  let isSceneOnTopB = window.localStorage.getItem('isSceneOnTop') || false
  let isVirtualCamActiveB
  let isIconModeB = window.localStorage.getItem('isIconMode') || false
  let isReplayingB
  let editableB = false
  let scenesB = []
  let replayErrorB = ''
  let errorMessageB = ''
  let imageFormatB = 'jpg'

  $: isSceneOnTop
    ? window.localStorage.setItem('isSceneOnTop', 'true')
    : window.localStorage.removeItem('isSceneOnTop')

  $: isIconMode
    ? window.localStorage.setItem('isIconMode', 'true')
    : window.localStorage.removeItem('isIconMode')
  
  $: isSceneOnTopB
    ? window.localStorage.setItem('isSceneOnTop', 'true')
    : window.localStorage.removeItem('isSceneOnTop')

  $: isIconModeB
    ? window.localStorage.setItem('isIconMode', 'true')
    : window.localStorage.removeItem('isIconMode')

  function formatTime (secs) {
    secs = Math.round(secs / 1000)
    const hours = Math.floor(secs / 3600)
    secs -= hours * 3600
    const mins = Math.floor(secs / 60)
    secs -= mins * 60
    return hours > 0
      ? `${hours}:${mins < 10 ? '0' : ''}${mins}:${secs < 10 ? '0' : ''}${secs}`
      : `${mins < 10 ? '0' : ''}${mins}:${secs < 10 ? '0' : ''}${secs}`
  }

  function toggleFullScreen () {
    if (isFullScreen) {
      if (document.exitFullscreen) {
        document.exitFullscreen()
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen()
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen()
      }
    } else {
      if (document.documentElement.requestFullscreen) {
        document.documentElement.requestFullscreen()
      } else if (document.documentElement.webkitRequestFullscreen) {
        document.documentElement.webkitRequestFullscreen()
      } else if (document.documentElement.msRequestFullscreen) {
        document.documentElement.msRequestFullscreen()
      }
    }
  }
  function toggleFullScreenB () {
    if (isFullScreenB) {
      if (document.exitFullscreen) {
        document.exitFullscreen()
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen()
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen()
      }
    } else {
      if (document.documentElement.requestFullscreen) {
        document.documentElement.requestFullscreen()
      } else if (document.documentElement.webkitRequestFullscreen) {
        document.documentElement.webkitRequestFullscreen()
      } else if (document.documentElement.msRequestFullscreen) {
        document.documentElement.msRequestFullscreen()
      }
    }
  }

  async function toggleStudioMode () {
    await sendCommand('SetStudioModeEnabled', {
      studioModeEnabled: !isStudioMode
    })
  }
  async function toggleStudioModeB () {
    await sendCommandB('SetStudioModeEnabled', {
      studioModeEnabled: !isStudioModeB
    })
  }

  async function toggleReplay () {
    const data = await sendCommand('ToggleReplayBuffer')
    console.debug('ToggleReplayBuffer', data.outputActive)
    if (data.outputActive === undefined) {
      replayError = 'Replay buffer is not enabled.'
      setTimeout(function () {
        replayError = ''
      }, 5000)
    } else isReplaying = data.outputActive
  }
  async function toggleReplayB () {
    const data = await sendCommandB('ToggleReplayBuffer')
    console.debug('ToggleReplayBuffer', data.outputActive)
    if (data.outputActive === undefined) {
      replayError = 'Replay buffer is not enabled.'
      setTimeout(function () {
        replayError = ''
      }, 5000)
    } else isReplayingB = data.outputActive
  }

  async function switchSceneView () {
    isSceneOnTop = !isSceneOnTop
  }
  async function switchSceneViewB () {
    isSceneOnTopB = !isSceneOnTopB
  }

  async function startStream () {
    await sendCommand('StartStream')
  }
  async function startStreamB () {
    await sendCommandB('StartStream')
  }

  async function stopStream () {
    await sendCommand('StopStream')
  }
  async function stopStreamB () {
    await sendCommandB('StopStream')
  }

  async function startRecording () {
    await sendCommand('StartRecord')
  }
  async function startRecordingB () {
    await sendCommandB('StartRecord')
  }

  async function stopRecording () {
    await sendCommand('StopRecord')
  }
  async function stopRecordingB () {
    await sendCommandB('StopRecord')
  }

  async function startVirtualCam () {
    await sendCommand('StartVirtualCam')
  }
  async function startVirtualCamB () {
    await sendCommandB('StartVirtualCam')
  }

  async function stopVirtualCam () {
    await sendCommand('StopVirtualCam')
  }
  async function stopVirtualCamB () {
    await sendCommandB('StopVirtualCam')
  }

  async function pauseRecording () {
    await sendCommand('PauseRecord')
  }
  async function pauseRecordingB () {
    await sendCommandB('PauseRecord')
  }

  async function resumeRecording () {
    await sendCommand('ResumeRecord')
  }
  async function resumeRecordingB () {
    await sendCommandB('ResumeRecord')
  }

  // Fungsi koneksi
async function connect() {
    address = address || 'ws://localhost:4455';
    if (address.indexOf('://') === -1) {
        const secure = location.protocol === 'https:' || address.endsWith(':443');
        address = (secure ? 'wss://' : 'ws://') + address;
    }

    console.log('Connecting to:', address, '- using password:', password);
    await disconnect();

    try {
        const { obsWebSocketVersion, negotiatedRpcVersion } = await obs.connect(address, password);
        console.log(`Connected to obs-websocket version ${obsWebSocketVersion} (using RPC ${negotiatedRpcVersion})`);
        connectionStatus = `Connected`;
    } catch (e) {
        console.log(e);
        errorMessage = e.message;
        connectionStatus = 'Error connecting to OBS: ' + errorMessage;
    }
}

async function connectB() {
    address = address || 'ws://localhost:4455';
    if (address.indexOf('://') === -1) {
        const secure = location.protocol === 'https:' || address.endsWith(':443');
        address = (secure ? 'wss://' : 'ws://') + address;
    }

    console.log('Connecting to:', address, '- using password:', password);
    await disconnectB();

    try {
        const { obsWebSocketVersion, negotiatedRpcVersion } = await obsB.connect(address, password);
        console.log(`Connected to obs-websocket version ${obsWebSocketVersion} (using RPC ${negotiatedRpcVersion})`);
        connectionStatusB = `Connected`;
    } catch (e) {
        console.log(e);
        errorMessageB = e.message;
        connectionStatusB = 'Error connecting to OBS B: ' + errorMessageB;
    }
}

async function switchConnection(connection) {
    selectedConnection = connection;
    ({ address, password } = connection); // Update address dan password dari connection
    connectionStatus = 'Connecting...'; // Status sementara
    await connect();
  }
  // Fungsi untuk beralih koneksi saat dropdown dipilih
  async function switchConnectionB(connection) {
    selectedConnectionB = connection;
    ({ address, password } = connection); // Update address dan password dari connection
    connectionStatusB = 'Connecting...'; // Status sementara
    await connectB();
  }
  
//   // GET ITEM FROM LOCAL STORAGE
// async function switchConnection(connection) {
//     selectedConnection = connection;
//     ({ address, password } = connection);
//     localStorage.setItem('selectedConnection', connections.indexOf(connection)); // Simpan ke localStorage
//     connectionStatus = 'Connecting...';
//     await connect();
// }

// async function switchConnectionB(connection) {
//     selectedConnectionB = connection;
//     ({ address, password } = connection);
//     localStorage.setItem('selectedConnectionB', connections.indexOf(connection)); // Simpan ke localStorage
//     connectionStatusB = 'Connecting...';
//     await connectB();
// }


  // Fungsi untuk memutuskan koneksi
  async function disconnect() {
    if (obs) {
      await obs.disconnect(); // Pastikan untuk memanggil disconnect dari objek obs
      clearInterval(heartbeatInterval)
      connectionStatus = 'Disconnected From Last Session';
      console.log('Disconnected from OBS A.');
    }
  }
  async function disconnectB() {
    if (obsB) {
      await obsB.disconnect(); // Pastikan untuk memanggil disconnect dari objek obs
      clearInterval(heartbeatIntervalB)
      connectionStatusB = 'Disconnected From Last Session';
      console.log('Disconnected from OBS.');
    }
  }

  // OBS events
  obs.on('ConnectionClosed', () => {
    connected = false
    window.history.pushState(
      '',
      document.title,
      window.location.pathname + window.location.search
    ) // Remove the hash
    console.log('Connection closed')
  })
  // OBS events
  obsB.on('ConnectionClosed', () => {
    connectedB = false
    window.history.pushState(
      '',
      document.title,
      window.location.pathname + window.location.search
    ) // Remove the hash
    console.log('Connection closed')
  })

  obs.on('Identified', async () => {
    console.log('Connected')
    connected = true
    document.location.hash = address // For easy bookmarking
    const data = await sendCommand('GetVersion')
    const version = data.obsWebSocketVersion || ''
    console.log('OBS-websocket version:', version)
    if (compareVersions(version, OBS_WEBSOCKET_LATEST_VERSION) < 0) {
      alert(
        'You are running an outdated OBS-websocket (version ' +
          version +
          '), please upgrade to the latest version for full compatibility.'
      )
    }
    if (
      data.supportedImageFormats.includes('webp') &&
      document
        .createElement('canvas')
        .toDataURL('image/webp')
        .indexOf('data:image/webp') === 0
    ) {
      imageFormat = 'webp'
    }
    heartbeatInterval = setInterval(async () => {
      const stats = await sendCommand('GetStats')
      const streaming = await sendCommand('GetStreamStatus')
      const recording = await sendCommand('GetRecordStatus')
      heartbeat = { stats, streaming, recording }
      // console.log(heartbeat);
    }, 1000) // Heartbeat
    isStudioMode =
      (await sendCommand('GetStudioModeEnabled')).studioModeEnabled || false
    isVirtualCamActive =
      (await sendCommand('GetVirtualCamStatus')).outputActive || false
  })

  obsB.on('Identified', async () => {
    console.log('Connected')
    connectedB = true
    document.location.hash = address // For easy bookmarking
    const data = await sendCommandB('GetVersion')
    const version = data.obsWebSocketVersion || ''
    console.log('OBS-websocket version:', version)
    if (compareVersions(version, OBS_WEBSOCKET_LATEST_VERSION) < 0) {
      alert(
        'You are running an outdated OBS-websocket (version ' +
          version +
          '), please upgrade to the latest version for full compatibility.'
      )
    }
    if (
      data.supportedImageFormats.includes('webp') &&
      document
        .createElement('canvas')
        .toDataURL('image/webp')
        .indexOf('data:image/webp') === 0
    ) {
      imageFormat = 'webp'
    }
    heartbeatIntervalB = setInterval(async () => {
      const stats = await sendCommandB('GetStats')
      const streaming = await sendCommandB('GetStreamStatus')
      const recording = await sendCommandB('GetRecordStatus')
      heartbeatB = { stats, streaming, recording }
      // console.log(heartbeat);
    }, 1000) // Heartbeat
    isStudioModeB =
      (await sendCommandB('GetStudioModeEnabled')).studioModeEnabled || false
    isVirtualCamActiveB =
      (await sendCommandB('GetVirtualCamStatus')).outputActive || false
  })

  obs.on('ConnectionError', async () => {
    errorMessage = 'Please enter your password:'
    document.getElementById('password').focus()
    if (!password) {
      connected = false
    } else {
      await connect()
    }
  })
  obsB.on('ConnectionError', async () => {
    errorMessageB = 'Please enter your password:'
    document.getElementById('password').focus()
    if (!password) {
      connectedB = false
    } else {
      await connectB()
    }
  })

  obs.on('VirtualcamStateChanged', async (data) => {
    console.log('VirtualcamStateChanged', data.outputActive)
    isVirtualCamActive = data && data.outputActive
  })
  obsB.on('VirtualcamStateChanged', async (data) => {
    console.log('VirtualcamStateChanged', data.outputActive)
    isVirtualCamActiveB = data && data.outputActive
  })

  obs.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioMode = data && data.studioModeEnabled
  })
  obsB.on('StudioModeStateChanged', async (data) => {
    console.log('StudioModeStateChanged', data.studioModeEnabled)
    isStudioModeB = data && data.studioModeEnabled
  })

  obs.on('ReplayBufferStateChanged', async (data) => {
    console.log('ReplayBufferStateChanged', data)
    isReplaying = data && data.outputActive
  })
  obsB.on('ReplayBufferStateChanged', async (data) => {
    console.log('ReplayBufferStateChanged', data)
    isReplayingB = data && data.outputActive
  })
  
</script>

<svelte:head>
  <title>OBS-web remote control</title>
</svelte:head>

<div class="row">
  <div class="column left">
    <div class="buttons">
      
      <!-- svelte-ignore a11y-missing-attribute -->
      {#if connected}
        <button class="button is-info is-light" disabled>
          {#if heartbeat && heartbeat.stats}
            {Math.round(heartbeat.stats.activeFps)} fps, {Math.round(
              heartbeat.stats.cpuUsage
            )}% CPU, {heartbeat.stats.renderSkippedFrames} skipped frames
          {:else}Connected{/if}
        </button>
        {#if heartbeat && heartbeat.streaming && heartbeat.streaming.outputActive}
          <button
            class="button is-danger"
            on:click={stopStream}
            title="Stop Stream"
          >
            <span class="icon"><Icon path={mdiAccessPointOff} /></span>
            <span>{formatTime(heartbeat.streaming.outputDuration)}</span>
          </button>
        {:else}
          <button
            class="button is-danger is-light"
            on:click={startStream}
            title="Start Stream"
          >
            <span class="icon"><Icon path={mdiAccessPoint} /></span>
          </button>
        {/if}
        <!-- {#if heartbeat && heartbeat.recording && heartbeat.recording.outputActive}
          {#if heartbeat.recording.outputPaused}
            <button
              class="button is-danger"
              on:click={resumeRecording}
              title="Resume Recording"
            >
              <span class="icon"><Icon path={mdiPlayPause} /></span>
            </button>
          {:else}
            <button
              class="button is-success"
              on:click={pauseRecording}
              title="Pause Recording"
            >
              <span class="icon"><Icon path={mdiPause} /></span>
            </button>
          {/if}
          <button
            class="button is-danger"
            on:click={stopRecording}
            title="Stop Recording"
          >
            <span class="icon"><Icon path={mdiStop} /></span>
            <span>{formatTime(heartbeat.recording.outputDuration)}</span>
          </button>
        {:else}
          <button
            class="button is-danger is-light"
            on:click={startRecording}
            title="Start Recording"
          >
            <span class="icon"><Icon path={mdiRecord} /></span>
          </button>
        {/if}
        {#if isVirtualCamActive}
          <button
            class="button is-danger"
            on:click={stopVirtualCam}
            title="Stop Virtual Webcam"
          >
            <span class="icon"><Icon path={mdiCameraOff} /></span>
          </button>
        {:else}
          <button
            class="button is-danger is-light"
            on:click={startVirtualCam}
            title="Start Virtual Webcam"
          >
            <span class="icon"><Icon path={mdiCamera} /></span>
          </button>
        {/if} -->
        <button
          class:is-light={!isStudioMode}
          class="button is-link"
          on:click={toggleStudioMode}
          title="Toggle Studio Mode"
        >
          <span class="icon"><Icon path={mdiBorderVertical} /></span>
        </button>
        <!-- <button
          class:is-light={!isSceneOnTop}
          class="button is-link"
          on:click={switchSceneView}
          title="Show Scene on Top"
        >
          <span class="icon"><Icon path={mdiArrowSplitHorizontal} /></span>
        </button>
        <button
          class:is-light={!editable}
          class="button is-link"
          title="Edit Scenes"
          on:click={() => (editable = !editable)}
        >
          <span class="icon">
            <Icon path={editable ? mdiImageEditOutline : mdiImageEdit} />
          </span>
        </button>
        <button
          class:is-light={!isIconMode}
          class="button is-link"
          title="Show Scenes as Icons"
          on:click={() => (isIconMode = !isIconMode)}
        >
          <span class="icon">
            <Icon
              path={isIconMode
                ? mdiSquareRoundedBadgeOutline
                : mdiSquareRoundedBadge}
            />
          </span>
        </button>
        <button
          class:is-light={!isReplaying}
          class:is-danger={replayError}
          class="button is-link"
          title="Toggle Replay Buffer"
          on:click={toggleReplay}
        >
          <span class="icon">
            <Icon
              path={isReplaying ? mdiMotionPlayOutline : mdiMotionPlay}
            />
          </span>
          {#if replayError}<span>{replayError}</span>{/if}
        </button> -->
        <ProfileSelect />
        <SceneCollectionSelect />
        <button
          class="button is-danger is-light"
          on:click={disconnect}
          title="Disconnect"
        >
          <span class="icon"><Icon path={mdiConnection} /></span>
        </button>
      {:else}
        <button class="button is-danger" disabled
          >{errorMessage || 'Disconnected'}</button
        >
      {/if}
      <!-- svelte-ignore a11y-missing-attribute -->
      <!-- <button
        class:is-light={!isFullScreen}
        class="button is-link"
        on:click={toggleFullScreen}
        title="Toggle Fullscreen"
      >
        <span class="icon">
          <Icon path={isFullScreen ? mdiFullscreenExit : mdiFullscreen} />
        </span>
      </button> -->
    </div>
  <!-- Tampilan Navbar dengan Dropdown untuk Beralih OBS -->
  <div class="dropdown">
    <!-- svelte-ignore a11y-missing-attribute -->
    <a class="navbar-link">Switch OBS</a>
      <div class="dropdown-content">
          <div class="column">
              <h3>Event - Primary</h3>
              {#each connections.filter(conn => conn.category === 'primary') as connection (connection.address)}
                  {#if !connection.hidden}
                      <!-- svelte-ignore a11y-click-events-have-key-events -->
                      <!-- svelte-ignore a11y-no-static-element-interactions -->
                      <!-- svelte-ignore a11y-missing-attribute -->
                      <a on:click={() => switchConnection(connection)}>
                          {connection.name}
                      </a>
                  {/if}
              {/each}
          </div>
          <div class="column">
              <h3>Event - Backup</h3>
              {#each connections.filter(conn => conn.category === 'backup') as connection (connection.address)}
                  <!-- svelte-ignore a11y-click-events-have-key-events -->
                  <!-- svelte-ignore a11y-no-static-element-interactions -->
                  <!-- svelte-ignore a11y-missing-attribute -->
                  {#if !connection.hidden}
                      <!-- svelte-ignore a11y-click-events-have-key-events -->
                      <!-- svelte-ignore a11y-no-static-element-interactions -->
                      <a on:click={() => switchConnection(connection)}>
                          {connection.name}
                      </a>
                  {/if}
              {/each}
          </div>
          <div class="column">
              <h3>TV - Primary</h3>
              {#each connections.filter(conn => conn.category === 'tv') as connection (connection.address)}
                  {#if !connection.hidden}
                      <!-- svelte-ignore a11y-click-events-have-key-events -->
                      <!-- svelte-ignore a11y-no-static-element-interactions -->
                      <!-- svelte-ignore a11y-missing-attribute -->
                      <a on:click={() => switchConnection(connection)}>
                          {connection.name}
                      </a>
                  {/if}
              {/each}
          </div>
          <div class="column">
              <h3>TV - Backup</h3>
              {#each connections.filter(conn => conn.category === 'tv-backup') as connection (connection.address)}
                  {#if !connection.hidden}
                      <!-- svelte-ignore a11y-click-events-have-key-events -->
                      <!-- svelte-ignore a11y-no-static-element-interactions -->
                      <!-- svelte-ignore a11y-missing-attribute -->
                      <a on:click={() => switchConnection(connection)}>
                          {connection.name}
                      </a>
                  {/if}
              {/each}
          </div>
  </div>
</div>
    <!-- Tampilan Utama -->
<section class="sections">
  <div class="containers">
    {#if connectionStatus === 'Disconnected'}
      <!-- <h1 class="title">OBS Web Control</h1>
      <p class="subtitle">
        Status: <strong>{connectionStatus}</strong>
      </p> -->
      <div class="box">
        <h2 class="is-size-4">Current OBS Server</h2>
        <p><strong>Name:</strong> N/A</p>
        <p><strong>Address:</strong> N/A</p>
        <!-- <p><strong>Password:</strong> N/A</p> -->
      </div>
    {:else}
      <!-- <h1 class="title">OBS Web Control</h1>
      <p class="subtitle">
        Status: <strong>{connectionStatus}</strong>
      </p> -->
      <div class="box">
        <h2 class="is-size-4">Current OBS Server</h2>
        <p><strong>Name:</strong> {selectedConnection.name}</p>
        <p><strong>Address:</strong> {address}</p>
        <!-- <p><strong>Password:</strong> {password ? 'Set' : 'Not Set'}</p> -->
        <p><strong>Status:</strong> {connectionStatus}</p>
        <!-- {#if errorMessage}
          <p class="has-text-danger"><strong>Error:</strong> {errorMessage}</p>
        {/if} -->
      </div>
    {/if}
    {#if connected}
      {#if isSceneOnTop}
        <ProgramPreview {imageFormat} />
      {/if}
      <SceneSwitcher
        bind:scenes
        buttonStyle={isIconMode ? 'icon' : 'text'}
        {editable}
      />
      {#if !isSceneOnTop}
        <ProgramPreview {imageFormat} />
      {/if}
      {#each scenes as scene}
        {#if scene.sceneName.indexOf('(switch)') > 0}
          <SourceSwitcher
            name={scene.sceneName}
            {imageFormat}
            buttonStyle="screenshot"
          />
        {/if}
      {/each}
    {:else}
      <!-- <h1 class="subtitle"><center>
        Welcome to
        <strong>OBS-web</strong>
        - the easiest way to control
        <a href="https://obsproject.com/" target="_blank" rel="noreferrer"
          >OBS</a
        >
        remotely!
      </h1>

      {#if document.location.protocol === 'https:'}
        <div class="notification is-danger">
          You are checking this page on a secure HTTPS connection. That's great,
          but it means you can
          <strong>only</strong>
          connect to WSS (secure websocket) addresses, for example OBS exposed with
          <a href="https://ngrok.com/">ngrok</a>
          or
          <a href="https://pagekite.net/">pagekite</a>
          . If you want to connect to a local OBS instance,
          <strong>
            <a
              href="http://{document.location.hostname}{document.location.port
                ? ':' + document.location.port
                : ''}{document.location.pathname}"
            >
              please click here to load the non-secure version of this page
            </a>
          </strong>
          .
        </div>
      {/if}

      
      <p class="help">
        Make sure that you use <a
          href="https://github.com/obsproject/obs-studio/releases">OBS v28+</a
        >
        or install the
        <a
          href="https://github.com/obsproject/obs-websocket/releases/tag/{OBS_WEBSOCKET_LATEST_VERSION}"
          target="_blank"
          rel="noreferrer"
          >obs-websocket {OBS_WEBSOCKET_LATEST_VERSION} plugin</a
        >
        for v27. If you use an older version of OBS, see the
        <a href="/v4/">archived OBS-web v4</a> page.
      </p> -->
    {/if}
  </div>
</section>
</div>

<div class="column middle">
  <!-- <div class="box">
  </div> -->
  <section class="sections">
    <div class="containers">
    <div class="multi">
      {#if connected && connectedB}
      {#if isStudioMode && isStudioModeB}<center>Multi <p>Switch</p></center>
          <ProgramPreviewMulti {imageFormat} />
      {/if}
      {/if}
    </div>
    </div>
  </section>
</div>

  <div class="column right">
    <div class="buttons">
        
      <!-- svelte-ignore a11y-missing-attribute -->
      {#if connectedB}
        <button class="button is-info is-light" disabled>
          {#if heartbeatB && heartbeatB.stats}
            {Math.round(heartbeatB.stats.activeFps)} fps, {Math.round(
              heartbeatB.stats.cpuUsage
            )}% CPU, {heartbeatB.stats.renderSkippedFrames} skipped frames
          {:else}Connected{/if}
        </button>
        {#if heartbeatB && heartbeatB.streaming && heartbeatB.streaming.outputActive}
          <button
            class="button is-danger"
            on:click={stopStreamB}
            title="Stop Stream"
          >
            <span class="icon"><Icon path={mdiAccessPointOff} /></span>
            <span>{formatTime(heartbeatB.streaming.outputDuration)}</span>
          </button>
        {:else}
          <button
            class="button is-danger is-light"
            on:click={startStreamB}
            title="Start Stream"
          >
            <span class="icon"><Icon path={mdiAccessPoint} /></span>
          </button>
        {/if}
        <!-- {#if heartbeatB && heartbeatB.recording && heartbeatB.recording.outputActive}
          {#if heartbeatB.recording.outputPaused}
            <button
              class="button is-danger"
              on:click={resumeRecordingB}
              title="Resume Recording"
            >
              <span class="icon"><Icon path={mdiPlayPause} /></span>
            </button>
          {:else}
            <button
              class="button is-success"
              on:click={pauseRecordingB}
              title="Pause Recording"
            >
              <span class="icon"><Icon path={mdiPause} /></span>
            </button>
          {/if}
          <button
            class="button is-danger"
            on:click={stopRecordingB}
            title="Stop Recording"
          >
            <span class="icon"><Icon path={mdiStop} /></span>
            <span>{formatTime(heartbeatB.recording.outputDuration)}</span>
          </button>
        {:else}
          <button
            class="button is-danger is-light"
            on:click={startRecordingB}
            title="Start Recording"
          >
            <span class="icon"><Icon path={mdiRecord} /></span>
          </button>
        {/if}
        {#if isVirtualCamActiveB}
          <button
            class="button is-danger"
            on:click={stopVirtualCamB}
            title="Stop Virtual Webcam"
          >
            <span class="icon"><Icon path={mdiCameraOff} /></span>
          </button>
        {:else}
          <button
            class="button is-danger is-light"
            on:click={startVirtualCamB}
            title="Start Virtual Webcam"
          >
            <span class="icon"><Icon path={mdiCamera} /></span>
          </button>
        {/if} -->
        <button
          class:is-light={!isStudioModeB}
          class="button is-link"
          on:click={toggleStudioModeB}
          title="Toggle Studio Mode"
        >
          <span class="icon"><Icon path={mdiBorderVertical} /></span>
        </button>
        <!-- <button
          class:is-light={!isSceneOnTopB}
          class="button is-link"
          on:click={switchSceneViewB}
          title="Show Scene on Top"
        >
          <span class="icon"><Icon path={mdiArrowSplitHorizontal} /></span>
        </button>
        <button
          class:is-light={!editableB}
          class="button is-link"
          title="Edit Scenes"
          on:click={() => (editableB = !editableB)}
        >
          <span class="icon">
            <Icon path={editableB ? mdiImageEditOutline : mdiImageEdit} />
          </span>
        </button>
        <button
          class:is-light={!isIconModeB}
          class="button is-link"
          title="Show Scenes as Icons"
          on:click={() => (isIconModeB = !isIconModeB)}
        >
          <span class="icon">
            <Icon
              path={isIconModeB
                ? mdiSquareRoundedBadgeOutline
                : mdiSquareRoundedBadge}
            />
          </span>
        </button>
        <button
          class:is-light={!isReplayingB}
          class:is-danger={replayErrorB}
          class="button is-link"
          title="Toggle Replay Buffer"
          on:click={toggleReplayB}
        >
          <span class="icon">
            <Icon
              path={isReplayingB ? mdiMotionPlayOutline : mdiMotionPlay}
            />
          </span>
          {#if replayErrorB}<span>{replayErrorB}</span>{/if}
        </button> -->
        <ProfileSelectB />
        <SceneCollectionSelectB />
        <button
          class="button is-danger is-light"
          on:click={disconnectB}
          title="Disconnect"
        >
          <span class="icon"><Icon path={mdiConnection} /></span>
        </button>
      {:else}
        <button class="button is-danger" disabled
          >{errorMessageB || 'Disconnected'}</button
        >
      {/if}
      <!-- svelte-ignore a11y-missing-attribute -->
      <!-- <button
        class:is-light={!isFullScreenB}
        class="button is-link"
        on:click={toggleFullScreenB}
        title="Toggle Fullscreen"
      >
        <span class="icon">
          <Icon path={isFullScreenB ? mdiFullscreenExit : mdiFullscreen} />
        </span>
      </button> -->
        
    </div>
    <div class="dropdown">
      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="navbar-link">Switch OBS B</a>
        <div class="dropdown-content">
            <div class="column">
                <h3>Event - Primary</h3>
                {#each connections.filter(conn => conn.category === 'primary') as connection (connection.address)}
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-missing-attribute -->
                    {#if !connection.hidden}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <!-- svelte-ignore a11y-no-static-element-interactions -->
                        <a on:click={() => switchConnectionB(connection)}>
                            {connection.name}
                        </a>
                    {/if}
                {/each}
            </div>
            <div class="column">
                <h3>Event - Backup</h3>
                {#each connections.filter(conn => conn.category === 'backup') as connection (connection.address)}
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-missing-attribute -->
                    {#if !connection.hidden}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <a on:click={() => switchConnectionB(connection)}>
                            {connection.name}
                        </a>
                    {/if}
                {/each}
            </div>
            <div class="column">
                <h3>TV - Primary</h3>
                {#each connections.filter(conn => conn.category === 'tv') as connection (connection.address)}
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-missing-attribute -->
                    {#if !connection.hidden}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <a on:click={() => switchConnectionB(connection)}>
                            {connection.name}
                        </a>
                    {/if}
                {/each}
            </div>
            <div class="column">
                <h3>TV - Backup</h3>
                {#each connections.filter(conn => conn.category === 'tv-backup') as connection (connection.address)}
                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-no-static-element-interactions -->
                    <!-- svelte-ignore a11y-missing-attribute -->
                    {#if !connection.hidden}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <a on:click={() => switchConnectionB(connection)}>
                            {connection.name}
                        </a>
                    {/if}
                {/each}
            </div>
    </div>
  </div>
    <!-- Tampilan Utama -->
<section class="sections">
  <div class="containers">
    
    {#if connectionStatusB === 'Disconnected'}
      <!-- <h1 class="title">OBS Web Control</h1>
      <p class="subtitle">
        Status: <strong>{connectionStatus}</strong>
      </p> -->
      <div class="box">
        <h2 class="is-size-4">Current OBS Server B</h2>
        <p><strong>Name:</strong> N/A</p>
        <p><strong>Address:</strong> N/A</p>
        <!-- <p><strong>Password:</strong> N/A</p> -->
      </div>
    {:else}
      <!-- <h1 class="title">OBS Web Control</h1>
      <p class="subtitle">
        Status: <strong>{connectionStatus}</strong>
      </p> -->
      <div class="box">
        <h2 class="is-size-4">Current OBS Server B</h2>
        <p><strong>Name:</strong> {selectedConnectionB.name}</p>
        <p><strong>Address:</strong> {address}</p>
        <!-- <p><strong>Password:</strong> {password ? 'Set' : 'Not Set'}</p> -->
        <p><strong>Status:</strong> {connectionStatusB}</p>
        <!-- {#if errorMessage}
          <p class="has-text-danger"><strong>Error:</strong> {errorMessage}</p>
        {/if} -->
      </div>
    {/if}
    <!-- test -->
      {#if connectedB}
      {#if isSceneOnTopB}
        <ProgramPreviewB {imageFormat} />
      {/if}
      <SceneSwitcherB
        bind:scenesB
        buttonStyle={isIconModeB ? 'icon' : 'text'}
        {editableB}
      />
      {#if !isSceneOnTopB}
        <ProgramPreviewB {imageFormat} />
      {/if}
      {#each scenesB as scene}
        {#if scene.sceneName.indexOf('(switch)') > 0}
          <SourceSwitcherB
            name={scene.sceneName}
            {imageFormat}
            buttonStyle="screenshot"
          />
        {/if}
      {/each}
    {:else}
      <!-- <h1 class="subtitle"><center>
        Welcome to
        <strong>OBS-web</strong>
        - the easiest way to control
        <a href="https://obsproject.com/" target="_blank" rel="noreferrer"
          >OBS</a
        >
        remotely!
      </h1>

      {#if document.location.protocol === 'https:'}
        <div class="notification is-danger">
          You are checking this page on a secure HTTPS connection. That's great,
          but it means you can
          <strong>only</strong>
          connect to WSS (secure websocket) addresses, for example OBS exposed with
          <a href="https://ngrok.com/">ngrok</a>
          or
          <a href="https://pagekite.net/">pagekite</a>
          . If you want to connect to a local OBS instance,
          <strong>
            <a
              href="http://{document.location.hostname}{document.location.port
                ? ':' + document.location.port
                : ''}{document.location.pathname}"
            >
              please click here to load the non-secure version of this page
            </a>
          </strong>
          .
        </div>
      {/if}

      
      <p class="help">
        Make sure that you use <a
          href="https://github.com/obsproject/obs-studio/releases">OBS v28+</a
        >
        or install the
        <a
          href="https://github.com/obsproject/obs-websocket/releases/tag/{OBS_WEBSOCKET_LATEST_VERSION}"
          target="_blank"
          rel="noreferrer"
          >obs-websocket {OBS_WEBSOCKET_LATEST_VERSION} plugin</a
        >
        for v27. If you use an older version of OBS, see the
        <a href="/v4/">archived OBS-web v4</a> page.
      </p> -->
    {/if}
    
  </div>
</section>
</div>
</div>

    <!-- svelte-ignore a11y-missing-attribute -->
    <!-- <button
      class="navbar-burger burger"
      aria-label="menu"
      aria-expanded="false"
      data-target="navmenu"
    >
      <span aria-hidden="true" />
      <span aria-hidden="true" />
      <span aria-hidden="true" />
    </button> -->

  




    
    <footer class="footer">
      <div class="content has-text-centered">
        <p>
          <strong>OBS-web</strong>
          by
          <a href="https://niekvandermaas.nl/">Niek van der Maas</a>
          &mdash; see
          <a href="https://github.com/Niek/obs-web">GitHub</a>
          for source code.
        </p>
      </div>
    </footer>




<!-- <section class="section">
  <div class="container">
    {#if connected && connectedB}
        <ProgramPreviewMulti {imageFormat} />
    {/if}
  </div>
</section> -->
