<script>
  /* eslint-env browser */
  const OBS_WEBSOCKET_LATEST_VERSION = "5.0.1"; // https://api.github.com/repos/Palakis/obs-websocket/releases/latest

  // Imports
  import { onMount } from "svelte";
  let connections = [
    {category: 'primary',   address: 'ws://192.168.40.178:4444',  hidden: false,  name: 'WG 100 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.178:4445',  hidden: false,  name: 'WG 100 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.178:4446',  hidden: false,  name: 'WG 100 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.177:4444',  hidden: false,  name: 'WG 101 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.177:4445',  hidden: false,  name: 'WG 101 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.177:4446',  hidden: false,  name: 'WG 101 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.33:4444',   hidden: false,  name: 'WG 102 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.33:4445',   hidden: false,  name: 'WG 102 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.33:4446',   hidden: false,  name: 'WG 102 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.187:4444',  hidden: false,  name: 'WG 103 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.187:4445',  hidden: false,  name: 'WG 103 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.187:4446',  hidden: false,  name: 'WG 103 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.46:4444',   hidden: false,  name: 'WG 104 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.46:4445',   hidden: false,  name: 'WG 104 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.46:4446',   hidden: false,  name: 'WG 104 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.45:4444',   hidden: false,  name: 'WG 105 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.45:4445',   hidden: false,  name: 'WG 105 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.45:4446',   hidden: false,  name: 'WG 105 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.19:4444',   hidden: false,  name: 'WG 106 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.19:4445',   hidden: false,  name: 'WG 106 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.19:4446',   hidden: false,  name: 'WG 106 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.186:4444',  hidden: false,  name: 'WG 107 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.186:4445',  hidden: false,  name: 'WG 107 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.186:4446',  hidden: false,  name: 'WG 107 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.49:4444',   hidden: false,  name: 'WG 108 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.49:4445',   hidden: false,  name: 'WG 108 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.49:4446',   hidden: false,  name: 'WG 108 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.185:4444',  hidden: false,  name: 'WG 109 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.185:4445',  hidden: false,  name: 'WG 109 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.185:4446',  hidden: false,  name: 'WG 109 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.48:4444',   hidden: false,  name: 'WG 110 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.48:4445',   hidden: false,  name: 'WG 110 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.48:4446',   hidden: false,  name: 'WG 110 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.184:4444',  hidden: false,  name: 'WG 111 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.184:4445',  hidden: false,  name: 'WG 111 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.184:4446',  hidden: false,  name: 'WG 111 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.51:4444',   hidden: false,  name: 'WG 112 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.51:4445',   hidden: false,  name: 'WG 112 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.51:4446',   hidden: false,  name: 'WG 112 - Event 3 (P)'},
    {category: 'backup',    address: 'ws://192.168.40.55:4444',   hidden: false,  name: 'WG 113 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.55:4445',   hidden: false,  name: 'WG 113 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.55:4446',   hidden: false,  name: 'WG 113 - Event 3 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.18:4444',   hidden: false,  name: 'WG 114 - Event 1 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.18:4445',   hidden: false,  name: 'WG 114 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.18:4446',   hidden: false,  name: 'WG 114 - Event 3 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.56:4444',   hidden: false,  name: 'WG 01 - SCTV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.56:4445',   hidden: false,  name: 'WG 01 - IVM (P)'},
    {category: 'tv',        address: 'ws://192.168.40.56:4446',   hidden: false,  name: 'WG 01 - MOJI (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.56:4444',   hidden: false,  name: 'WG 02 - SCTV (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.56:4445',   hidden: false,  name: 'WG 02 - IVM (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.56:4446',   hidden: false,  name: 'WG 02 - MOJI (B)'},
    {category: 'tv',        address: 'ws://192.168.40.14:4444',   hidden: false,  name: 'WG 03 - CTV 1 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.14:4445',   hidden: false,  name: 'WG 03 - CTV 2 (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.188:4444',  hidden: false,  name: 'WG 04 - CTV 1 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.188:4445',  hidden: false,  name: 'WG 04 - CTV 2 (B)'},
    {category: 'tv',        address: 'ws://192.168.40.181:4444',  hidden: false,  name: 'WG 18 - CTV 1 Serie A (P)'},
    {category: 'tv',        address: 'ws://192.168.40.181:4445',  hidden: false,  name: 'WG 18 - CTV 2 Serie A (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.179:4444',  hidden: false,  name: 'WG 12 - CTV 1 Serie A (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.179:4445',  hidden: false,  name: 'WG 12 - CTV 2 Serie A (B)'},
    {category: 'tv',        address: 'ws://192.168.40.13:4445',   hidden: false,  name: 'WG 05 - PLTV (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.194:4445',  hidden: false,  name: 'WG 06 - PLTV (B)'},
    {category: 'tv',        address: 'ws://192.168.40.12:4446',   hidden: false,  name: 'WG 07 - Trans 7 (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.137:4446',  hidden: false,  name: 'WG 08 - Trans 7 (B)'},
    {category: 'tv',        address: 'ws://192.168.40.11:4445',   hidden: false,  name: 'WG 09 - TVN (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.192:4445',  hidden: false,  name: 'WG 10 - TVN (B)'},
    {category: 'tv',        address: 'ws://192.168.40.40:4444',   hidden: false,  name: 'WG 26 - CTV 5 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.40:4445',   hidden: false,  name: 'WG 26 - CTV 6 (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.190:4444',  hidden: false,  name: 'WG 27 - CTV 5 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.190:4445',  hidden: false,  name: 'WG 27 - CTV 6 (B)'},
    {category: 'tv',        address: 'ws://192.168.40.67:4444',   hidden: false,  name: 'WG 28 - SPOTV 1 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.67:4445',   hidden: false,  name: 'WG 28 - SPOTV 2 (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.189:4444',  hidden: false,  name: 'WG 29 - SPOTV 1 (P)'},
    {category: 'tv-backup', address: 'ws://192.168.40.189:4445',  hidden: false,  name: 'WG 29 - SPOTV 2 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.43:4444',   hidden: false,  name: 'WG 30 - beIN 1 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.43:4445',   hidden: false,  name: 'WG 30 - beIN 2 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.43:4446',   hidden: false,  name: 'WG 30 - F1 Event (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.78:4444',   hidden: false,  name: 'WG 31 - beIN 1 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.78:4445',   hidden: false,  name: 'WG 31 - beIN 2 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.78:4446',   hidden: false,  name: 'WG 31 - F1 Event (B)'},
    {category: 'tv',        address: 'ws://192.168.40.36:4445',   hidden: false,  name: 'WG 19 - Mentari TV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.10:4444',   hidden: false,  name: 'WG 20 - NET TV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.35:4445',   hidden: false,  name: 'WG 22 - RTV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.65:4444',   hidden: false,  name: 'WG 23 - ANTV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.65:4445',   hidden: false,  name: 'WG 23 - TV One (P)'},
  ];
  // GET ITEM FROM LOCAL STORAGE
  // let selectedConnection = connections[localStorage.getItem('selectedConnection') || 0];
  // let selectedConnectionB = connections[localStorage.getItem('selectedConnection') || 1];

  let selectedConnection = connections[0]; // Default connection
  let selectedConnectionB = connections[1];

  let connectionStatus = "Disconnected";
  let connectionStatusB = "Disconnected";

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
    mdiMotionPlay,
  } from "@mdi/js";
  import Icon from "mdi-svelte";
  import { compareVersions } from "compare-versions";

  import "./style.scss";
  import { obs, sendCommand, obsB, sendCommandB } from "./obs.js";
  import ProgramPreview from "./ProgramPreview.svelte";
  import ProgramPreviewMulti from "./ProgramPreviewMulti.svelte";
  import SceneSwitcher from "./SceneSwitcher.svelte";
  import SourceSwitcher from "./SourceSwitcher.svelte";
  import ProfileSelect from "./ProfileSelect.svelte";
  import SceneCollectionSelect from "./SceneCollectionSelect.svelte";
  import ProgramPreviewB from "./ProgramPreviewB.svelte";
  import SceneSwitcherB from "./SceneSwitcherB.svelte";
  import SourceSwitcherB from "./SourceSwitcherB.svelte";
  import ProfileSelectB from "./ProfileSelectB.svelte";
  import SceneCollectionSelectB from "./SceneCollectionSelectB.svelte";

  onMount(async () => {
    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("/service-worker.js");
    }

    // Request screen wakelock
    if ("wakeLock" in navigator) {
      try {
        await navigator.wakeLock.request("screen");
        // Re-request when coming back
        document.addEventListener("visibilitychange", async () => {
          if (document.visibilityState === "visible") {
            await navigator.wakeLock.request("screen");
          }
        });
      } catch (e) {}
    }

    // Toggle the navigation hamburger menu on mobile
    const navbar = document.querySelector(".navbar-burger");
    navbar.addEventListener("click", () => {
      navbar.classList.toggle("is-active");
      document
        .getElementById(navbar.dataset.target)
        .classList.toggle("is-active");
    });

    // Listen for fullscreen changes
    document.addEventListener("fullscreenchange", () => {
      isFullScreen = document.fullscreenElement;
    });

    document.addEventListener("webkitfullscreenchange", () => {
      isFullScreen = document.webkitFullscreenElement;
    });

    document.addEventListener("msfullscreenchange", () => {
      isFullScreen = document.msFullscreenElement;
    });

    if (document.location.hash !== "") {
      // Read address from hash
      address = document.location.hash.slice(1);

      // This allows you to add a password in the URL like this:
      // http://obs-web.niek.tv/#ws://localhost:4455#password
      if (address.includes("#")) {
        [address, password] = address.split("#");
      }
      await connect();
    }

    // Export the sendCommand() function to the window objects
    window.sendCommand = sendCommand;
  });

  onMount(async () => {
    if ("serviceWorker" in navigator) {
      navigator.serviceWorker.register("/service-worker.js");
    }

    // Request screen wakelock
    if ("wakeLock" in navigator) {
      try {
        await navigator.wakeLock.request("screen");
        // Re-request when coming back
        document.addEventListener("visibilitychange", async () => {
          if (document.visibilityState === "visible") {
            await navigator.wakeLock.request("screen");
          }
        });
      } catch (e) {}
    }

    // Toggle the navigation hamburger menu on mobile
    const navbar = document.querySelector(".navbar-burger");
    navbar.addEventListener("click", () => {
      navbar.classList.toggle("is-active");
      document
        .getElementById(navbar.dataset.target)
        .classList.toggle("is-active");
    });

    // Listen for fullscreen changes
    document.addEventListener("fullscreenchange", () => {
      isFullScreenB = document.fullscreenElement;
    });

    document.addEventListener("webkitfullscreenchange", () => {
      isFullScreenB = document.webkitFullscreenElement;
    });

    document.addEventListener("msfullscreenchange", () => {
      isFullScreenB = document.msFullscreenElement;
    });

    if (document.location.hash !== "") {
      // Read address from hash
      address = document.location.hash.slice(1);

      // This allows you to add a password in the URL like this:
      // http://obs-web.niek.tv/#ws://localhost:4455#password
      if (address.includes("#")) {
        [address, password] = address.split("#");
      }
      await connectB();
    }

    // Export the sendCommand() function to the window objects
    window.sendCommand = sendCommandB;
  });

  // State
  let connected;
  let heartbeat = {};
  let heartbeatInterval;
  let isFullScreen;
  let isStudioMode;
  let isSceneOnTop = window.localStorage.getItem("isSceneOnTop") || false;
  let isVirtualCamActive;
  let isIconMode = window.localStorage.getItem("isIconMode") || false;
  let isReplaying;
  let editable = false;
  let scenes = [];
  let replayError = "";
  let errorMessage = "";
  let imageFormat = "jpg";

  // State B
  let connectedB;
  let heartbeatB = {};
  let heartbeatIntervalB;
  let isFullScreenB;
  let isStudioModeB;
  let isSceneOnTopB = window.localStorage.getItem("isSceneOnTop") || false;
  let isVirtualCamActiveB;
  let isIconModeB = window.localStorage.getItem("isIconMode") || false;
  let isReplayingB;
  let editableB = false;
  let scenesB = [];
  let replayErrorB = "";
  let errorMessageB = "";
  let imageFormatB = "jpg";

  $: isSceneOnTop
    ? window.localStorage.setItem("isSceneOnTop", "true")
    : window.localStorage.removeItem("isSceneOnTop");

  $: isIconMode
    ? window.localStorage.setItem("isIconMode", "true")
    : window.localStorage.removeItem("isIconMode");

  $: isSceneOnTopB
    ? window.localStorage.setItem("isSceneOnTop", "true")
    : window.localStorage.removeItem("isSceneOnTop");

  $: isIconModeB
    ? window.localStorage.setItem("isIconMode", "true")
    : window.localStorage.removeItem("isIconMode");

  function formatTime(secs) {
    secs = Math.round(secs / 1000);
    const hours = Math.floor(secs / 3600);
    secs -= hours * 3600;
    const mins = Math.floor(secs / 60);
    secs -= mins * 60;
    return hours > 0
      ? `${hours}:${mins < 10 ? "0" : ""}${mins}:${secs < 10 ? "0" : ""}${secs}`
      : `${mins < 10 ? "0" : ""}${mins}:${secs < 10 ? "0" : ""}${secs}`;
  }

  function toggleFullScreen() {
    if (isFullScreen) {
      if (document.exitFullscreen) {
        document.exitFullscreen();
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen();
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen();
      }
    } else {
      if (document.documentElement.requestFullscreen) {
        document.documentElement.requestFullscreen();
      } else if (document.documentElement.webkitRequestFullscreen) {
        document.documentElement.webkitRequestFullscreen();
      } else if (document.documentElement.msRequestFullscreen) {
        document.documentElement.msRequestFullscreen();
      }
    }
  }
  function toggleFullScreenB() {
    if (isFullScreenB) {
      if (document.exitFullscreen) {
        document.exitFullscreen();
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen();
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen();
      }
    } else {
      if (document.documentElement.requestFullscreen) {
        document.documentElement.requestFullscreen();
      } else if (document.documentElement.webkitRequestFullscreen) {
        document.documentElement.webkitRequestFullscreen();
      } else if (document.documentElement.msRequestFullscreen) {
        document.documentElement.msRequestFullscreen();
      }
    }
  }

  async function toggleStudioMode() {
    await sendCommand("SetStudioModeEnabled", {
      studioModeEnabled: !isStudioMode,
    });
  }
  async function toggleStudioModeB() {
    await sendCommandB("SetStudioModeEnabled", {
      studioModeEnabled: !isStudioModeB,
    });
  }

  async function toggleReplay() {
    const data = await sendCommand("ToggleReplayBuffer");
    console.debug("ToggleReplayBuffer", data.outputActive);
    if (data.outputActive === undefined) {
      replayError = "Replay buffer is not enabled.";
      setTimeout(function () {
        replayError = "";
      }, 5000);
    } else isReplaying = data.outputActive;
  }
  async function toggleReplayB() {
    const data = await sendCommandB("ToggleReplayBuffer");
    console.debug("ToggleReplayBuffer", data.outputActive);
    if (data.outputActive === undefined) {
      replayError = "Replay buffer is not enabled.";
      setTimeout(function () {
        replayError = "";
      }, 5000);
    } else isReplayingB = data.outputActive;
  }

  async function switchSceneView() {
    isSceneOnTop = !isSceneOnTop;
  }
  async function switchSceneViewB() {
    isSceneOnTopB = !isSceneOnTopB;
  }

  async function startStream() {
    await sendCommand("StartStream");
  }
  async function startStreamB() {
    await sendCommandB("StartStream");
  }

  async function stopStream() {
    await sendCommand("StopStream");
  }
  async function stopStreamB() {
    await sendCommandB("StopStream");
  }

  async function startRecording() {
    await sendCommand("StartRecord");
  }
  async function startRecordingB() {
    await sendCommandB("StartRecord");
  }

  async function stopRecording() {
    await sendCommand("StopRecord");
  }
  async function stopRecordingB() {
    await sendCommandB("StopRecord");
  }

  async function startVirtualCam() {
    await sendCommand("StartVirtualCam");
  }
  async function startVirtualCamB() {
    await sendCommandB("StartVirtualCam");
  }

  async function stopVirtualCam() {
    await sendCommand("StopVirtualCam");
  }
  async function stopVirtualCamB() {
    await sendCommandB("StopVirtualCam");
  }

  async function pauseRecording() {
    await sendCommand("PauseRecord");
  }
  async function pauseRecordingB() {
    await sendCommandB("PauseRecord");
  }

  async function resumeRecording() {
    await sendCommand("ResumeRecord");
  }
  async function resumeRecordingB() {
    await sendCommandB("ResumeRecord");
  }

  async function checkObsVersion() {
  const { version } = await sendCommand('GetVersion'); 
  if (version >= 30) {
    imageFormat = 'png';
  } else {
    imageFormat = 'jpg';
  }
}

  async function connect() {
    address = address || "ws://localhost:4455";
    if (address.indexOf("://") === -1) {
      const secure = location.protocol === "https:" || address.endsWith(":443");
      address = (secure ? "wss://" : "ws://") + address;
    }

    console.log("Connecting to:", address, "- using password:", password);
    await checkObsVersion();
    await disconnect();

    try {
      const { obsWebSocketVersion, negotiatedRpcVersion } = await obs.connect(
        address,
        password,
      );
      console.log(
        `Connected to obs-websocket version ${obsWebSocketVersion} (using RPC ${negotiatedRpcVersion})`,
      );
      connectionStatus = `Connected`;
    } catch (e) {
      console.log(e);
      errorMessage = e.message;
      connectionStatus = "Error connecting to OBS: " + errorMessage;
    }
  }

  async function connectB() {
    address = address || "ws://localhost:4455";
    if (address.indexOf("://") === -1) {
      const secure = location.protocol === "https:" || address.endsWith(":443");
      address = (secure ? "wss://" : "ws://") + address;
    }

    console.log("Connecting to:", address, "- using password:", password);
    await checkObsVersion();
    await disconnectB();

    try {
      const { obsWebSocketVersion, negotiatedRpcVersion } = await obsB.connect(
        address,
        password,
      );
      console.log(
        `Connected to obs-websocket version ${obsWebSocketVersion} (using RPC ${negotiatedRpcVersion})`,
      );
      connectionStatusB = `Connected`;
    } catch (e) {
      console.log(e);
      errorMessageB = e.message;
      connectionStatusB = "Error connecting to OBS B: " + errorMessageB;
    }
  }

  async function switchConnection(connection) {
    selectedConnection = connection;
    ({ address, password } = connection); 
    connectionStatus = "Connecting...";
    await connect();
  }
  async function switchConnectionB(connection) {
    selectedConnectionB = connection;
    ({ address, password } = connection); 
    connectionStatusB = "Connecting...";
    await connectB();
  }

  //   // GET ITEM FROM LOCAL STORAGE
  // async function switchConnection(connection) {
  //     selectedConnection = connection;
  //     ({ address, password } = connection);
  //     localStorage.setItem('selectedConnection', connections.indexOf(connection));
  //     connectionStatus = 'Connecting...';
  //     await connect();
  // }

  // async function switchConnectionB(connection) {
  //     selectedConnectionB = connection;
  //     ({ address, password } = connection);
  //     localStorage.setItem('selectedConnectionB', connections.indexOf(connection));
  //     connectionStatusB = 'Connecting...';
  //     await connectB();
  // }

  async function disconnect() {
    if (obs) {
      await obs.disconnect();
      clearInterval(heartbeatInterval);
      connectionStatus = "Disconnected From Last Session";
      console.log("Disconnected from OBS A.");
    }
  }
  async function disconnectB() {
    if (obsB) {
      await obsB.disconnect();
      clearInterval(heartbeatIntervalB);
      connectionStatusB = "Disconnected From Last Session";
      console.log("Disconnected from OBS.");
    }
  }

  // OBS events
  obs.on("ConnectionClosed", () => {
    connected = false;
    window.history.pushState(
      "",
      document.title,
      window.location.pathname + window.location.search,
    ); // Remove the hash
    console.log("Connection closed");
  });
  // OBS events
  obsB.on("ConnectionClosed", () => {
    connectedB = false;
    window.history.pushState(
      "",
      document.title,
      window.location.pathname + window.location.search,
    ); // Remove the hash
    console.log("Connection closed");
  });

  obs.on("Identified", async () => {
    console.log("Connected");
    connected = true;
    document.location.hash = address; // For easy bookmarking
    const data = await sendCommand("GetVersion");
    const version = data.obsWebSocketVersion || "";
    console.log("OBS-websocket version:", version);
    if (compareVersions(version, OBS_WEBSOCKET_LATEST_VERSION) < 0) {
      alert(
        "You are running an outdated OBS-websocket (version " +
          version +
          "), please upgrade to the latest version for full compatibility.",
      );
    }
    if (
      data.supportedImageFormats.includes("webp") &&
      document
        .createElement("canvas")
        .toDataURL("image/webp")
        .indexOf("data:image/webp") === 0
    ) {
      imageFormat = "webp";
    }
    heartbeatInterval = setInterval(async () => {
      const stats = await sendCommand("GetStats");
      const streaming = await sendCommand("GetStreamStatus");
      const recording = await sendCommand("GetRecordStatus");
      heartbeat = { stats, streaming, recording };
      // console.log(heartbeat);
    }, 1000); // Heartbeat
    isStudioMode =
      (await sendCommand("GetStudioModeEnabled")).studioModeEnabled || false;
    isVirtualCamActive =
      (await sendCommand("GetVirtualCamStatus")).outputActive || false;
  });

  obsB.on("Identified", async () => {
    console.log("Connected");
    connectedB = true;
    document.location.hash = address; // For easy bookmarking
    const data = await sendCommandB("GetVersion");
    const version = data.obsWebSocketVersion || "";
    console.log("OBS-websocket version:", version);
    if (compareVersions(version, OBS_WEBSOCKET_LATEST_VERSION) < 0) {
      alert(
        "You are running an outdated OBS-websocket (version " +
          version +
          "), please upgrade to the latest version for full compatibility.",
      );
    }
    if (
      data.supportedImageFormats.includes("webp") &&
      document
        .createElement("canvas")
        .toDataURL("image/webp")
        .indexOf("data:image/webp") === 0
    ) {
      imageFormat = "webp";
    }
    heartbeatIntervalB = setInterval(async () => {
      const stats = await sendCommandB("GetStats");
      const streaming = await sendCommandB("GetStreamStatus");
      const recording = await sendCommandB("GetRecordStatus");
      heartbeatB = { stats, streaming, recording };
      // console.log(heartbeat);
    }, 1000); // Heartbeat
    isStudioModeB =
      (await sendCommandB("GetStudioModeEnabled")).studioModeEnabled || false;
    isVirtualCamActiveB =
      (await sendCommandB("GetVirtualCamStatus")).outputActive || false;
  });

  obs.on("ConnectionError", async () => {
    errorMessage = "Please enter your password:";
    document.getElementById("password").focus();
    if (!password) {
      connected = false;
    } else {
      await connect();
    }
  });
  obsB.on("ConnectionError", async () => {
    errorMessageB = "Please enter your password:";
    document.getElementById("password").focus();
    if (!password) {
      connectedB = false;
    } else {
      await connectB();
    }
  });

  obs.on("VirtualcamStateChanged", async (data) => {
    console.log("VirtualcamStateChanged", data.outputActive);
    isVirtualCamActive = data && data.outputActive;
  });
  obsB.on("VirtualcamStateChanged", async (data) => {
    console.log("VirtualcamStateChanged", data.outputActive);
    isVirtualCamActiveB = data && data.outputActive;
  });

  obs.on("StudioModeStateChanged", async (data) => {
    console.log("StudioModeStateChanged", data.studioModeEnabled);
    isStudioMode = data && data.studioModeEnabled;
  });
  obsB.on("StudioModeStateChanged", async (data) => {
    console.log("StudioModeStateChanged", data.studioModeEnabled);
    isStudioModeB = data && data.studioModeEnabled;
  });

  obs.on("ReplayBufferStateChanged", async (data) => {
    console.log("ReplayBufferStateChanged", data);
    isReplaying = data && data.outputActive;
  });
  obsB.on("ReplayBufferStateChanged", async (data) => {
    console.log("ReplayBufferStateChanged", data);
    isReplayingB = data && data.outputActive;
  });
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
              heartbeat.stats.cpuUsage,
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
          >{errorMessage || "Disconnected"}</button
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
    <div class="dropdown">
      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="navbar-link">Switch OBS</a>
      <div class="dropdown-content">
        <div class="column">
          <h3>Event - Primary</h3>
          {#each connections.filter((conn) => conn.category === "primary") as connection (connection.address)}
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
          {#each connections.filter((conn) => conn.category === "backup") as connection (connection.address)}
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
          {#each connections.filter((conn) => conn.category === "tv") as connection (connection.address)}
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
          {#each connections.filter((conn) => conn.category === "tv-backup") as connection (connection.address)}
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
    <section class="sections">
      <div class="containers">
        {#if connectionStatus === "Disconnected"}
          <div class="box">
            <h2 class="is-size-4">Current OBS Server</h2>
            <p><strong>Name:</strong> N/A</p>
          </div>
        {:else}
          <div class="box">
            <h2 class="is-size-4">Current OBS Server</h2>
            <p><strong>Name:</strong> {selectedConnection.name}</p>
            <p><strong>Status:</strong> {connectionStatus}</p>
          </div>
        {/if}
        {#if connected}
          {#if isSceneOnTop}
            <ProgramPreview {imageFormat} />
          {/if}
          <SceneSwitcher
            bind:scenes
            buttonStyle={isIconMode ? "icon" : "text"}
            {editable}
          />
          {#if !isSceneOnTop}
            <ProgramPreview {imageFormat} />
          {/if}
          {#each scenes as scene}
            {#if scene.sceneName.indexOf("(switch)") > 0}
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
    <section class="sections">
      <div class="containers">
        <div class="multi">
          {#if connected && connectedB}
            {#if isStudioMode && isStudioModeB}<center
                >Multi <p>Switch</p></center
              >
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
              heartbeatB.stats.cpuUsage,
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
          >{errorMessageB || "Disconnected"}</button
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
          {#each connections.filter((conn) => conn.category === "primary") as connection (connection.address)}
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
          {#each connections.filter((conn) => conn.category === "backup") as connection (connection.address)}
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
          {#each connections.filter((conn) => conn.category === "tv") as connection (connection.address)}
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
          {#each connections.filter((conn) => conn.category === "tv-backup") as connection (connection.address)}
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
        {#if connectionStatusB === "Disconnected"}
          <div class="box">
            <h2 class="is-size-4">Current OBS Server B</h2>
            <p><strong>Name:</strong> N/A</p>
          </div>
        {:else}
          <div class="box">
            <h2 class="is-size-4">Current OBS Server B</h2>
            <p><strong>Name:</strong> {selectedConnectionB.name}</p>
            <p><strong>Status:</strong> {connectionStatusB}</p>
          </div>
        {/if}
        {#if connectedB}
          {#if isSceneOnTopB}
            <ProgramPreviewB {imageFormat} />
          {/if}
          <SceneSwitcherB
            bind:scenesB
            buttonStyle={isIconModeB ? "icon" : "text"}
            {editableB}
          />
          {#if !isSceneOnTopB}
            <ProgramPreviewB {imageFormat} />
          {/if}
          {#each scenesB as scene}
            {#if scene.sceneName.indexOf("(switch)") > 0}
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
