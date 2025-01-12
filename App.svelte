<script>
  /* eslint-env browser */
  const OBS_WEBSOCKET_LATEST_VERSION = "5.0.1"; // https://api.github.com/repos/Palakis/obs-websocket/releases/latest

  // Imports
  import { onMount } from "svelte";
  let connections = [

    // WIRECAST GEAR SENAYAN 100
    {category: 'primary',   address: 'ws://192.168.40.178:4444',  show: true,   name: 'WG 100 - FA Cup | Middlesbrough vs Blackburn Rovers | Brentford vs Plymouth Argyle | 15801 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.178:4445',  show: true,   name: 'WG 100 - FA Cup | Birmingham vs Lincoln City | Bournemouth vs West Bromwich Albion | 15860 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.178:4446',  show: true,   name: 'WG 100 - FA Cup | Nottm Forest vs Luton Town | 16884 (P)'},
    
    // WIRECAST GEAR SENAYAN 101
    {category: 'backup',    address: 'ws://192.168.40.177:4444',  show: true,   name: 'WG 101 - Middlesbrough vs Blackburn Rovers | Brentford vs Plymouth Argyle | 15801 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.177:4445',  show: true,   name: 'WG 101 - Birmingham vs Lincoln City | Bournemouth vs West Bromwich Albion | 15860 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.177:4446',  show: true,   name: 'WG 101 - FA Cup | Nottm Forest vs Luton Town | 16884 (B)'},
    
    // WIRECAST GEAR SENAYAN 102
    {category: 'primary',   address: 'ws://192.168.40.33:4444',   show: true,   name: 'WG 102 - Liga 1 | Dewa Utd v Arema | 17622 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.33:4445',   show: true,   name: 'WG 102 - Prematch | Dewa Utd v Arema | 17625 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.33:4446',   show: true,   name: 'WG 102 - FA Cup | Norwich v Brighton |16883 (P)'},
    
    // WIRECAST GEAR SENAYAN 103
    {category: 'backup',    address: 'ws://192.168.40.187:4444',  show: true,   name: 'WG 103 - Liga 1 | Dewa Utd v Arema | 17622 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.187:4445',  show: false,   name: 'WG 103 - Liga 2 | Bhayangkara vs Persijap | 17759 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.187:4446',  show: true,   name: 'WG 103 - FA Cup | Norwich v Brighton |16883 (B)'},
    
    // WIRECAST GEAR SENAYAN 104
    {category: 'primary',   address: 'ws://192.168.40.46:4444',   show: true,    name: 'WG 104 - FA Cup | Reading vs Burnley | 18255 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.46:4445',   show: true,    name: 'WG 104 - FA Cup | Exeter vs Oxford | 15861 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.46:4446',   show: true,    name: 'WG 104 - FA Cup | Sunderland vs Stoke | 18256 (P)'},
    
    // WIRECAST GEAR SENAYAN 105
    {category: 'backup',    address: 'ws://192.168.40.45:4444',   show: true,    name: 'WG 105 - FA Cup | Reading vs Burnley | 18255 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.45:4445',   show: true,    name: 'WG 105 - FA Cup | Exeter vs Oxford | 15861 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.45:4446',   show: true,    name: 'WG 105 - FA Cup | Sunderland vs Stoke | 18256 (B)'},
    
    // WIRECAST GEAR SENAYAN 106
    {category: 'primary',   address: 'ws://192.168.40.19:4444',   show: true,   name: 'WG 106 - PROLIGA 2025 | 18234 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.19:4445',   show: false,   name: 'WG 106 - Taoyuan Pauian Pilot vs Hiroshima Dragonflies | 17927 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.19:4446',   show: false,   name: 'WG 106 - Lugano vs Pafos | 18009 (P)'},
    
    // WIRECAST GEAR SENAYAN 107
    {category: 'backup',    address: 'ws://192.168.40.186:4444',  show: true,   name: 'WG 107 - PROLIGA 2025 | 18234 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.186:4445',  show: false,   name: 'WG 107 - Taoyuan Pauian Pilot vs Hiroshima Dragonflies | 17927 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.186:4446',  show: false,   name: 'WG 107 - Lugano vs Pafos | 18009 (B)'},
    
    // WIRECAST GEAR SENAYAN 108
    {category: 'primary',   address: 'ws://192.168.40.49:4444',   show: true,   name: 'WG 108 - BWF | 18249 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.49:4445',   show: true,   name: 'WG 108 - Turki Men | 17961 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.49:4446',   show: false,   name: 'WG 108 - Rapid Wien vs Copenhagen  | 18010 (P)'},
    
    // WIRECAST GEAR SENAYAN 109
    {category: 'backup',    address: 'ws://192.168.40.185:4444',  show: true,   name: 'WG 109 - BWF | 18249 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.185:4445',  show: true,   name: 'WG 109 - Turki Men | 17961 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.185:4446',  show: false,   name: 'WG 109 - Rapid Wien vs Copenhagen  | 18010 (B)'},
    
    // WIRECAST GEAR SENAYAN 110
    {category: 'primary',   address: 'ws://192.168.40.48:4444',   show: true,  name: 'WG 110 - LaLiga | 17320 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.48:4445',   show: false,  name: 'WG 110 - UECL | Petro dan Pafos | 18011 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.48:4446',   show: false,  name: 'WG 110 - IWF | 18148 (P)'},
    
    // WIRECAST GEAR SENAYAN 111
    {category: 'backup',    address: 'ws://192.168.40.184:4444',  show: true,  name: 'WG 111 - LaLiga | 17320 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.184:4445',  show: false,  name: 'WG 111 - UECL | Noah v Vikingur | 18008 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.184:4446',  show: false,  name: 'WG 111 - IWF | 18148  (B)'},
    
    // WIRECAST GEAR SENAYAN 112
    {category: 'primary',   address: 'ws://192.168.40.51:4444',   show: false,  name: 'WG 112 - MISA Emtek | 7466'},
    {category: 'primary',   address: 'ws://192.168.40.51:4445',   show: false,  name: 'WG 112 - UECL | Petro dan Pafos | 18011 (B)'},
    {category: 'primary',   address: 'ws://192.168.40.51:4446',   show: false,  name: 'WG 112 - Event 3 (P)'},
    
    // WIRECAST GEAR SENAYAN 113
    {category: 'backup',    address: 'ws://192.168.40.55:4444',   show: false,  name: 'WG 113 - Event 1 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.55:4445',   show: false,  name: 'WG 113 - Event 2 (B)'},
    {category: 'backup',    address: 'ws://192.168.40.55:4446',   show: false,  name: 'WG 113 - Event 3 (B)'},
    
    // WIRECAST GEAR SENAYAN 114
    {category: 'primary',   address: 'ws://192.168.40.18:4444',   show: false,  name: 'WG 114 - Testing PC Syauqi'},
    {category: 'primary',   address: 'ws://192.168.40.18:4445',   show: false,  name: 'WG 114 - Event 2 (P)'},
    {category: 'primary',   address: 'ws://192.168.40.18:4446',   show: false,  name: 'WG 114 - Event 3 (P)'},

    // WIRECAST GEAR SENAYAN 01
    {category: 'tv',        address: 'ws://192.168.40.56:4444',   show: true,  name: 'WG 01 - SCTV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.56:4445',   show: true,  name: 'WG 01 - IVM (P)'},
    {category: 'tv',        address: 'ws://192.168.40.56:4446',   show: true,  name: 'WG 01 - MOJI (P)'},

    // WIRECAST GEAR SENAYAN 02
    {category: 'tv-backup', address: 'ws://192.168.40.38:4444',   show: true,  name: 'WG 02 - SCTV (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.38:4445',   show: true,  name: 'WG 02 - IVM (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.38:4446',   show: true,  name: 'WG 02 - MOJI (B)'},

    // WIRECAST GEAR SENAYAN 03
    {category: 'tv',        address: 'ws://192.168.40.14:4444',   show: true,  name: 'WG 03 - CTV 1 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.14:4445',   show: true,  name: 'WG 03 - CTV 2 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.14:4446',   show: true,  name: 'WG 03 - Rock Extreme (P)'},

    // WIRECAST GEAR SENAYAN 04
    {category: 'tv-backup', address: 'ws://192.168.40.188:4444',  show: true,  name: 'WG 04 - CTV 1 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.188:4445',  show: true,  name: 'WG 04 - CTV 2 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.188:4446',  show: true,  name: 'WG 04 - Rock Extreme (B)'},

    // WIRECAST GEAR SENAYAN 05
    {category: 'tv',        address: 'ws://192.168.40.13:4444',   show: true,  name: 'WG 05 - CTV 3 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.13:4445',   show: true,  name: 'WG 05 - PLTV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.13:4446',   show: true,  name: 'WG 05 - Rock Entertainment (P)'},

    // WIRECAST GEAR SENAYAN 06
    {category: 'tv-backup', address: 'ws://192.168.40.194:4444',  show: true,  name: 'WG 06 - CTV 3 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.194:4445',  show: true,  name: 'WG 06 - PLTV (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.194:4446',  show: true,  name: 'WG 06 - Rock Entertainment (B)'},

    // WIRECAST GEAR SENAYAN 07
    {category: 'tv',        address: 'ws://192.168.40.12:4444',   show: true,  name: 'WG 07 - NBA TV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.12:4445',   show: true,  name: 'WG 07 - Trans TV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.12:4446',   show: true,  name: 'WG 07 - Trans 7 (P)'},

    // WIRECAST GEAR SENAYAN 08
    {category: 'tv-backup', address: 'ws://192.168.40.137:4444',  show: true,  name: 'WG 08 - NBA TV (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.137:4445',  show: true,  name: 'WG 08 - Trans TV (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.137:4446',  show: true,  name: 'WG 08 - Trans 7 (B)'},

    // WIRECAST GEAR SENAYAN 09
    {category: 'tv',        address: 'ws://192.168.40.11:4445',   show: true,  name: 'WG 09 - TVN (P)'},
    {category: 'tv',        address: 'ws://192.168.40.11:4446',   show: true,  name: 'WG 09 - Bein 2 (P)'},

    // WIRECAST GEAR SENAYAN 10
    {category: 'tv-backup', address: 'ws://192.168.40.192:4445',  show: true,  name: 'WG 10 - TVN (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.192:4446',  show: true,  name: 'WG 10 - Bein 2 (B)'},

    // WIRECAST GEAR SENAYAN 18
    {category: 'tv',        address: 'ws://192.168.40.181:4444',  show: true,  name: 'WG 18 - SERIE A | CTV 1(P)'},
    {category: 'tv',        address: 'ws://192.168.40.181:4445',  show: true,  name: 'WG 18 - SERIE A | CTV 2(P)'},
    {category: 'tv',        address: 'ws://192.168.40.181:4446',  show: true,  name: 'WG 18 - COPPA SERIA | CTV 1(P)'},

    // WIRECAST GEAR SENAYAN 12
    {category: 'tv-backup', address: 'ws://192.168.40.179:4444',  show: true,  name: 'WG 12 -  SERIE A  | CTV 1(B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.179:4445',  show: true,  name: 'WG 12 -  SERIE A  | CTV 2(B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.179:4446',  show: true,  name: 'WG 12 - COPPA SERIA | CTV 1(B)'},

    // WIRECAST GEAR SENAYAN 26
    {category: 'tv',        address: 'ws://192.168.40.40:4444',   show: true,  name: 'WG 26 - CTV 5 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.40:4445',   show: true,  name: 'WG 26 - CTV 6 (P)'},

    // WIRECAST GEAR SENAYAN 27
    {category: 'tv-backup', address: 'ws://192.168.40.190:4444',  show: true,  name: 'WG 27 - CTV 5 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.190:4445',  show: true,  name: 'WG 27 - CTV 6 (B)'},

    // WIRECAST GEAR SENAYAN 28
    {category: 'tv',        address: 'ws://192.168.40.67:4444',   show: true,  name: 'WG 28 - SPOTV 1 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.67:4445',   show: true,  name: 'WG 28 - SPOTV 2 (P)'},

    // WIRECAST GEAR SENAYAN 29
    {category: 'tv-backup', address: 'ws://192.168.40.189:4444',  show: true,  name: 'WG 29 - SPOTV 1 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.189:4445',  show: true,  name: 'WG 29 - SPOTV 2 (B)'},

    // WIRECAST GEAR SENAYAN 30
    {category: 'tv',        address: 'ws://192.168.40.43:4444',   show: true,  name: 'WG 30 - beIN 1 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.43:4445',   show: true,  name: 'WG 30 - beIN 3 (P)'},
    {category: 'tv',        address: 'ws://192.168.40.43:4446',   show: true,  name: 'WG 30 - F1 Event (P)'},

    // WIRECAST GEAR SENAYAN 31
    {category: 'tv-backup', address: 'ws://192.168.40.78:4444',   show: true,  name: 'WG 31 - beIN 1 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.78:4445',   show: true,  name: 'WG 31 - beIN 3 (B)'},
    {category: 'tv-backup', address: 'ws://192.168.40.78:4446',   show: true,  name: 'WG 31 - F1 Event (B)'},

    // WIRECAST GEAR SENAYAN 20
    {category: 'tv',        address: 'ws://192.168.40.10:4444',   show: true,  name: 'WG 20 - NET TV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.10:4445',   show: true,  name: 'WG 20 - RTV (P)'},

    // WIRECAST GEAR SENAYAN 22
    {category: 'tv',        address: 'ws://192.168.40.35:4445',   show: true,  name: 'WG 22 - Mentari TV (P)'},

    // WIRECAST GEAR SENAYAN 23
    {category: 'tv',        address: 'ws://192.168.40.65:4444',   show: true,  name: 'WG 23 - ANTV (P)'},
    {category: 'tv',        address: 'ws://192.168.40.65:4445',   show: true,  name: 'WG 23 - TV One (P)'},

    //Xpert-01
    {category: 'tv',        address: 'ws://192.168.40.27:4444',   show: true,  name: 'Xpert-01 - GGS (P)'},
    
  ];
  // GET ITEM FROM LOCAL STORAGE
  // let selectedConnection = connections[localStorage.getItem('selectedConnection') || 0];
  // let selectedConnectionB = connections[localStorage.getItem('selectedConnection') || 1];

  let selectedConnection = connections[0]; // Default connection
  let selectedConnectionB = connections[1];
  let selectedConnectionC = connections[2];
  let selectedConnectionD = connections[3];

  let connectionStatus = "Disconnected";
  let connectionStatusB = "Disconnected";
  let connectionStatusC = "Disconnected";
  let connectionStatusD = "Disconnected";

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
    mdiTrendingUp,
  } from "@mdi/js";
  import Icon from "mdi-svelte";
  import { compareVersions } from "compare-versions";

  import "./style.scss";
  import { obs, sendCommand, obsB, sendCommandB, obsD, sendCommandD } from "./obs.js";
  import ProgramPreview from "./ProgramPreview.svelte";
  import ProgramPreviewMulti from "./ProgramPreviewMulti.svelte";
  import ProgramPreviewMulti2 from "./ProgramPreviewMulti2.svelte";
  import SceneSwitcher from "./SceneSwitcher.svelte";
  import SourceSwitcher from "./SourceSwitcher.svelte";
  import ProfileSelect from "./ProfileSelect.svelte";
  import SceneCollectionSelect from "./SceneCollectionSelect.svelte";
  import ProgramPreviewB from "./ProgramPreviewB.svelte";
  import ProgramPreviewD from "./ProgramPreviewD.svelte";
  import SceneSwitcherB from "./SceneSwitcherB.svelte";
  import SceneSwitcherD from "./SceneSwitcherD.svelte";
  import SourceSwitcherB from "./SourceSwitcherB.svelte";
  import SourceSwitcherD from "./SourceSwitcherD.svelte";
  import ProfileSelectB from "./ProfileSelectB.svelte";
  import ProfileSelectD from "./ProfileSelectD.svelte";
  import SceneCollectionSelectB from "./SceneCollectionSelectB.svelte";
  import SceneCollectionSelectD from "./SceneCollectionSelectD.svelte";

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
    // const navbar = document.querySelector(".navbar-burger");
    // navbar.addEventListener("click", () => {
    //   navbar.classList.toggle("is-active");
    //   document
    //     .getElementById(navbar.dataset.target)
    //     .classList.toggle("is-active");
    // });

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

    document.addEventListener("fullscreenchange", () => {
      isFullScreenB = document.fullscreenElement;
    });

    document.addEventListener("webkitfullscreenchange", () => {
      isFullScreenB = document.webkitFullscreenElement;
    });

    document.addEventListener("msfullscreenchange", () => {
      isFullScreenB = document.msFullscreenElement;
    });
    
    document.addEventListener("fullscreenchange", () => {
      isFullScreenD = document.fullscreenElement;
    });

    document.addEventListener("webkitfullscreenchange", () => {
      isFullScreenD = document.webkitFullscreenElement;
    });

    document.addEventListener("msfullscreenchange", () => {
      isFullScreenD = document.msFullscreenElement;
    });
    // if (document.location.hash !== "") {
    //   // Read address from hash
    //   address = document.location.hash.slice(1);

    //   // This allows you to add a password in the URL like this:
    //   // http://obs-web.niek.tv/#ws://localhost:4455#password
    //   if (address.includes("#")) {
    //     [address, password] = address.split("#");
    //   }
    //   await connect();
    // }

    // Export the sendCommand() function to the window objects
    window.sendCommand = sendCommand;
    window.sendCommandB = sendCommandB;
    window.sendCommandD = sendCommandD;
    document.addEventListener('click', closeDropdown);
    document.addEventListener('click', closeDropdownB);
    document.addEventListener('click', closeDropdownD);
    return () => {
      document.removeEventListener('click', closeDropdown);
    return () => {
      document.removeEventListener('click', closeDropdownB);
    return () => {
      document.removeEventListener('click', closeDropdownD);
    };
    };
    };
  });

  // onMount(async () => {
  //   if ("serviceWorker" in navigator) {
  //     navigator.serviceWorker.register("/service-worker.js");
  //   }

  //   // Request screen wakelock
  //   if ("wakeLock" in navigator) {
  //     try {
  //       await navigator.wakeLock.request("screen");
  //       // Re-request when coming back
  //       document.addEventListener("visibilitychange", async () => {
  //         if (document.visibilityState === "visible") {
  //           await navigator.wakeLock.request("screen");
  //         }
  //       });
  //     } catch (e) {}
  //   }

  //   // Toggle the navigation hamburger menu on mobile
  //   const navbar = document.querySelector(".navbar-burger");
  //   // navbar.addEventListener("click", () => {
  //   //   navbar.classList.toggle("is-active");
  //   //   document
  //   //     .getElementById(navbar.dataset.target)
  //   //     .classList.toggle("is-active");
  //   // });

  //   // Listen for fullscreen changes
  //   document.addEventListener("fullscreenchange", () => {
  //     isFullScreenB = document.fullscreenElement;
  //   });

  //   document.addEventListener("webkitfullscreenchange", () => {
  //     isFullScreenB = document.webkitFullscreenElement;
  //   });

  //   document.addEventListener("msfullscreenchange", () => {
  //     isFullScreenB = document.msFullscreenElement;
  //   });

  //   // if (document.location.hash !== "") {
  //   //   // Read address from hash
  //   //   address = document.location.hash.slice(1);

  //   //   // This allows you to add a password in the URL like this:
  //   //   // http://obs-web.niek.tv/#ws://localhost:4455#password
  //   //   if (address.includes("#")) {
  //   //     [address, password] = address.split("#");
  //   //   }
  //   //   await connectB();
  //   // }

  //   // Export the sendCommand() function to the window objects
  //   window.sendCommand = sendCommandB;
  //   document.addEventListener('click', closeDropdownB);
  //   return () => {
  //     document.removeEventListener('click', closeDropdownB);
  //   };
  // });

  // State
  let isLeftFullscreen = false;
  let isRightFullscreen = false;
  let isBottomRightFullscreen = false;
  let isDropdownVisible = false;
  let isDropdownVisibleB = false;
  let isDropdownVisibleD = false;
  let selectedConnectionName = "Switch OBS";
  let selectedConnectionNameB = "Switch OBS";
  let selectedConnectionNameD = "Switch OBS";
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

  // State D
  let connectedD;
  let heartbeatD = {};
  let heartbeatIntervalD;
  let isFullScreenD;
  let isStudioModeD;
  let isSceneOnTopD = window.localStorage.getItem("isSceneOnTop") || false;
  let isVirtualCamActiveD;
  let isIconModeD = window.localStorage.getItem("isIconMode") || false;
  let isReplayingD;
  let editableD = false;
  let scenesD = [];
  let replayErrorD = "";
  let errorMessageD = "";
  let imageFormatD = "jpg";

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

  $: isSceneOnTopD
    ? window.localStorage.setItem("isSceneOnTop", "true")
    : window.localStorage.removeItem("isSceneOnTop");

  $: isIconModeD
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

  function selectConnection(connection) {
    selectedConnectionName = connection.name; // Update the dropdown name
    isDropdownVisible = false; // Close the dropdown
    switchConnection(connection); // Call the original connection switch logic
  }
  function selectConnectionB(connection) {
    selectedConnectionNameB = connection.name; // Update the dropdown name
    isDropdownVisibleB = false; // Close the dropdown
    switchConnectionB(connection); // Call the original connection switch logic
  }
  function selectConnectionD(connection) {
    selectedConnectionNameD = connection.name; // Update the dropdown name
    isDropdownVisibleD = false; // Close the dropdown
    switchConnectionD(connection); // Call the original connection switch logic
  }

  function toggleDropdown() {
    isDropdownVisible = !isDropdownVisible;
  }
  function toggleDropdownB() {
    isDropdownVisibleB = !isDropdownVisibleB;
  }
  function toggleDropdownD() {
    isDropdownVisibleD = !isDropdownVisibleD;
  }

  function closeDropdown(event) {
    const dropdown = document.querySelector('.dropdown');
    if (dropdown && !dropdown.contains(event.target)) {
      isDropdownVisible = false;
    }
  }
  function closeDropdownB(event) {
    const dropdownB = document.querySelector('.dropdownB');
    if (dropdownB && !dropdownB.contains(event.target)) {
      isDropdownVisibleB = false;
    }
  }
  function closeDropdownD(event) {
    const dropdownD = document.querySelector('.dropdownD');
    if (dropdownD && !dropdownD.contains(event.target)) {
      isDropdownVisibleD = false;
    }
  }

  // Fungsi untuk toggle fullscreen
  function toggleFullscreensLeft(column) {
    if (column === 'left') {
      isLeftFullscreen = !isLeftFullscreen;
    }
  }
  function toggleFullscreensRight(column) {
    if (column === 'right') {
      isRightFullscreen = !isRightFullscreen;
    }
  }
  function toggleFullscreensBottomRight(column) {
    if (column === 'right') {
      isBottomRightFullscreen = !isBottomRightFullscreen;
    }
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
  function toggleFullScreenD() {
    if (isFullScreenD) {
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
  async function toggleStudioModeD() {
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
  async function startStreamD() {
    await sendCommandD("StartStream");
  }

  async function stopStream() {
    await sendCommand("StopStream");
  }
  async function stopStreamB() {
    await sendCommandB("StopStream");
  }
  async function stopStreamD() {
    await sendCommandD("StopStream");
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
  async function checkObsVersionB() {
  const { version } = await sendCommandB('GetVersion'); 
  if (version >= 30) {
    imageFormatB = 'png';
  } else {
    imageFormatB = 'jpg';
  }
}
  async function checkObsVersionD() {
  const { version } = await sendCommandD('GetVersion'); 
  if (version >= 30) {
    imageFormatD = 'png';
  } else {
    imageFormatD = 'jpg';
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
    await checkObsVersionB();
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
  async function connectD() {
    address = address || "ws://localhost:4455";
    if (address.indexOf("://") === -1) {
      const secure = location.protocol === "https:" || address.endsWith(":443");
      address = (secure ? "wss://" : "ws://") + address;
    }

    console.log("Connecting to:", address, "- using password:", password);
    await checkObsVersionD();
    await disconnectD();

    try {
      const { obsWebSocketVersion, negotiatedRpcVersion } = await obsD.connect(
        address,
        password,
      );
      console.log(
        `Connected to obs-websocket version ${obsWebSocketVersion} (using RPC ${negotiatedRpcVersion})`,
      );
      connectionStatusD = `Connected`;
    } catch (e) {
      console.log(e);
      errorMessageD = e.message;
      connectionStatusD = "Error connecting to OBS D: " + errorMessageD;
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
  async function switchConnectionD(connection) {
    selectedConnectionD = connection;
    ({ address, password } = connection); 
    connectionStatusD = "Connecting...";
    await connectD();
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
  async function disconnectD() {
    if (obsD) {
      await obsD.disconnect();
      clearInterval(heartbeatIntervalD);
      connectionStatusD = "Disconnected From Last Session";
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
      imageFormatB = "webp";
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
  obsD.on("Identified", async () => {
    console.log("Connected");
    connectedD = true;
    document.location.hash = address; // For easy bookmarking
    const data = await sendCommandD("GetVersion");
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
      imageFormatD = "webp";
    }
    heartbeatIntervalD = setInterval(async () => {
      const stats = await sendCommandD("GetStats");
      const streaming = await sendCommandD("GetStreamStatus");
      const recording = await sendCommandD("GetRecordStatus");
      heartbeatD = { stats, streaming, recording };
      // console.log(heartbeat);
    }, 1000); // Heartbeat
    isStudioModeD =
      (await sendCommandD("GetStudioModeEnabled")).studioModeEnabled || false;
    isVirtualCamActiveD =
      (await sendCommandD("GetVirtualCamStatus")).outputActive || false;
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
  obsD.on("ConnectionError", async () => {
    errorMessageD = "Please enter your password:";
    document.getElementById("password").focus();
    if (!password) {
      connectedD = false;
    } else {
      await connectD();
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
  obsD.on("VirtualcamStateChanged", async (data) => {
    console.log("VirtualcamStateChanged", data.outputActive);
    isVirtualCamActiveD = data && data.outputActive;
  });

  obs.on("StudioModeStateChanged", async (data) => {
    console.log("StudioModeStateChanged", data.studioModeEnabled);
    isStudioMode = data && data.studioModeEnabled;
  });
  obsB.on("StudioModeStateChanged", async (data) => {
    console.log("StudioModeStateChanged", data.studioModeEnabled);
    isStudioModeB = data && data.studioModeEnabled;
  });
  obsD.on("StudioModeStateChanged", async (data) => {
    console.log("StudioModeStateChanged", data.studioModeEnabled);
    isStudioModeD = data && data.studioModeEnabled;
  });

  obs.on("ReplayBufferStateChanged", async (data) => {
    console.log("ReplayBufferStateChanged", data);
    isReplaying = data && data.outputActive;
  });
  obsB.on("ReplayBufferStateChanged", async (data) => {
    console.log("ReplayBufferStateChanged", data);
    isReplayingB = data && data.outputActive;
  });
  obsD.on("ReplayBufferStateChanged", async (data) => {
    console.log("ReplayBufferStateChanged", data);
    isReplayingD = data && data.outputActive;
  });
</script>

<svelte:head>
  <title>OBS-web remote control</title>
</svelte:head>

<div class="row">
  <div class="column left" class:fullscreen={isLeftFullscreen}>
    

    
    <section class="sections">
      <div class="container">
        <div class="box kiri">
          <div class="buttons">
            <div class="dropdown">
              <!-- Display the selected connection name -->
              <!-- svelte-ignore a11y-click-events-have-key-events -->
              <!-- svelte-ignore a11y-no-static-element-interactions -->
              <!-- svelte-ignore a11y-missing-attribute -->
              <a 
                class="navbar-link" 
                on:click={toggleDropdown} 
                aria-expanded={isDropdownVisible}
              >
                {selectedConnectionName}
              </a>
              
              {#if isDropdownVisible}
                <div class="dropdown-content">
                  {#each [
                    { title: "Event - Primary", category: "primary" },
                    { title: "Event - Backup", category: "backup" },
                    { title: "TV - Primary", category: "tv" },
                    { title: "TV - Backup", category: "tv-backup" }
                  ] as group}
                    <div class="column">
                      <h3>{group.title}</h3>
                      {#each connections.filter(conn => conn.category === group.category) as connection (connection.address)}
                        {#if connection.show}
                          <!-- svelte-ignore a11y-click-events-have-key-events -->
                          <!-- svelte-ignore a11y-no-static-element-interactions -->
                          <!-- svelte-ignore a11y-missing-attribute -->
                          <a on:click={() => selectConnection(connection)}>
                            {connection.name}
                          </a>
                        {/if}
                      {/each}
                    </div>
                  {/each}
                </div>
              {/if}
            </div>
              <!-- svelte-ignore a11y-missing-attribute -->
              {#if connected}
              <ProfileSelect />
              <SceneCollectionSelect />
              <button class="button is-info is-light" disabled>
                {#if heartbeat && heartbeat.stats}
                  {Math.round(heartbeat.stats.activeFps)} fps, {Math.round(
                    heartbeat.stats.cpuUsage,
                  )}% CPU, {heartbeat.stats.renderSkippedFrames} skipped frames
                {:else}Connected{/if}
              </button>
                <!-- <button class="button is-info is-light" disabled>{selectedConnection.name}</button> -->
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
                <button
                  class="button is-danger is-light"
                  on:click={disconnect}
                  title="Disconnect"
                >
                  <span class="icon"><Icon path={mdiConnection} /></span>
                </button>
                <button class="button is-link" on:click={() => toggleFullscreensLeft('left')}>Full Screen</button>
                <!-- <button class="button is-info is-light" disabled>{connectionStatus}</button> -->
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
          </div>
          <!-- <div class="boxx kanan">
          </div> -->
        </div>
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
    </section>
  </div>

  <div class="column middle">
    <section class="sections">
      <div class="containers">
        <div class="multi">
          {#if connected && connectedB}
            {#if isStudioMode && isStudioModeB}<center>Multi</center>
              <ProgramPreviewMulti {imageFormat} />
            {/if}
          {/if}
        </div>
      </div>
    </section>
  </div>

  <div class="column right" class:fullscreen={isRightFullscreen}>
    
    <!-- Tampilan Utama -->
    <section class="sections">
      <div class="container">
        <!-- {#if connectionStatusB === "Disconnected"} -->
        <div class="box kiri">
        <div class="buttons">
          <div class="dropdownB">
            <!-- Display the selected connection name -->
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <!-- svelte-ignore a11y-no-static-element-interactions -->
            <!-- svelte-ignore a11y-missing-attribute -->
            <a 
              class="navbar-link" 
              on:click={toggleDropdownB} 
              aria-expanded={isDropdownVisibleB}
            >
              {selectedConnectionNameB}
            </a>
            
            {#if isDropdownVisibleB}
              <div class="dropdown-content">
                {#each [
                  { title: "Event - Primary", category: "primary" },
                  { title: "Event - Backup", category: "backup" },
                  { title: "TV - Primary", category: "tv" },
                  { title: "TV - Backup", category: "tv-backup" }
                ] as group}
                  <div class="column">
                    <h3>{group.title}</h3>
                    {#each connections.filter(conn => conn.category === group.category) as connection (connection.address)}
                      {#if connection.show}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <!-- svelte-ignore a11y-no-static-element-interactions -->
                        <!-- svelte-ignore a11y-missing-attribute -->
                        <a on:click={() => selectConnectionB(connection)}>
                          {connection.name}
                        </a>
                      {/if}
                    {/each}
                  </div>
                {/each}
              </div>
            {/if}
          </div>
          <!-- svelte-ignore a11y-missing-attribute -->
          {#if connectedB}
          <ProfileSelectB />
          <SceneCollectionSelectB />
          <button class="button is-info is-light" disabled>
            {#if heartbeatB && heartbeatB.stats}
              {Math.round(heartbeatB.stats.activeFps)} fps, {Math.round(
                heartbeatB.stats.cpuUsage,
              )}% CPU, {heartbeatB.stats.renderSkippedFrames} skipped frames
            {:else}Connected{/if}
          </button>
            <!-- <button class="button is-info is-light" disabled>{selectedConnectionB.name}</button> -->
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
            <button
              class="button is-danger is-light"
              on:click={disconnectB}
              title="Disconnect"
            >
              <span class="icon"><Icon path={mdiConnection} /></span>
            </button>
            <button class="button is-link" on:click={() => toggleFullscreensRight('right')}>Full Screen</button>
            <!-- <button class="button is-info is-light" disabled>{connectionStatusB}</button> -->

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
        </div>
        <!-- hoverdisini -->
        <!-- {/if} -->

      </div>
      {#if connectedB}
          {#if isSceneOnTopB}
          <ProgramPreviewB {imageFormatB} />
          {/if}
          <SceneSwitcherB
          bind:scenesB
          buttonStyle={isIconModeB ? "icon" : "text"}
          {editableB}
          />
          {#if !isSceneOnTopB}
          <ProgramPreviewB {imageFormatB} />
          {/if}
          {#each scenesB as scene}
          {#if scene.sceneName.indexOf("(switch)") > 0}
          <SourceSwitcherB
          name={scene.sceneName}
          {imageFormatB}
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

<div class="row">
  <div class="column left" class:fullscreen={isLeftFullscreen}>
    

    
    <section class="sections">
      <div class="container">
        <div class="box kiri">
          <div class="buttons">
            <div class="dropdown">
              <!-- Display the selected connection name -->
              <!-- svelte-ignore a11y-click-events-have-key-events -->
              <!-- svelte-ignore a11y-no-static-element-interactions -->
              <!-- svelte-ignore a11y-missing-attribute -->
              <a 
                class="navbar-link" 
                on:click={toggleDropdown} 
                aria-expanded={isDropdownVisible}
              >
                {selectedConnectionName}
              </a>
              
              {#if isDropdownVisible}
                <div class="dropdown-content">
                  {#each [
                    { title: "Event - Primary", category: "primary" },
                    { title: "Event - Backup", category: "backup" },
                    { title: "TV - Primary", category: "tv" },
                    { title: "TV - Backup", category: "tv-backup" }
                  ] as group}
                    <div class="column">
                      <h3>{group.title}</h3>
                      {#each connections.filter(conn => conn.category === group.category) as connection (connection.address)}
                        {#if connection.show}
                          <!-- svelte-ignore a11y-click-events-have-key-events -->
                          <!-- svelte-ignore a11y-no-static-element-interactions -->
                          <!-- svelte-ignore a11y-missing-attribute -->
                          <a on:click={() => selectConnection(connection)}>
                            {connection.name}
                          </a>
                        {/if}
                      {/each}
                    </div>
                  {/each}
                </div>
              {/if}
            </div>
              <!-- svelte-ignore a11y-missing-attribute -->
              {#if connected}
              <ProfileSelect />
              <SceneCollectionSelect />
              <button class="button is-info is-light" disabled>
                {#if heartbeat && heartbeat.stats}
                  {Math.round(heartbeat.stats.activeFps)} fps, {Math.round(
                    heartbeat.stats.cpuUsage,
                  )}% CPU, {heartbeat.stats.renderSkippedFrames} skipped frames
                {:else}Connected{/if}
              </button>
                <!-- <button class="button is-info is-light" disabled>{selectedConnection.name}</button> -->
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
                <button
                  class="button is-danger is-light"
                  on:click={disconnect}
                  title="Disconnect"
                >
                  <span class="icon"><Icon path={mdiConnection} /></span>
                </button>
                <button class="button is-link" on:click={() => toggleFullscreensLeft('left')}>Full Screen</button>
                <!-- <button class="button is-info is-light" disabled>{connectionStatus}</button> -->
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
          </div>
          <!-- <div class="boxx kanan">
          </div> -->
        </div>
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
    </section>
  </div>

  <div class="column middle">
    <section class="sections">
      <div class="containers">
        <div class="multi">
          {#if connected && connectedD}
            {#if isStudioMode && isStudioModeD}<center>Multi</center>
              <ProgramPreviewMulti2 {imageFormat} />
            {/if}
          {/if}
        </div>
      </div>
    </section>
  </div>

  <div class="column right" class:fullscreen={isBottomRightFullscreen}>
    
    <!-- Tampilan Utama -->
    <section class="sections">
      <div class="container">
        <!-- {#if connectionStatusB === "Disconnected"} -->
        <div class="box kiri">
        <div class="buttons">
          <div class="dropdownD">
            <!-- Display the selected connection name -->
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <!-- svelte-ignore a11y-no-static-element-interactions -->
            <!-- svelte-ignore a11y-missing-attribute -->
            <a 
              class="navbar-link" 
              on:click={toggleDropdownD} 
              aria-expanded={isDropdownVisibleD}
            >
              {selectedConnectionNameD}
            </a>
            
            {#if isDropdownVisibleD}
              <div class="dropdown-content">
                {#each [
                  { title: "Event - Primary", category: "primary" },
                  { title: "Event - Backup", category: "backup" },
                  { title: "TV - Primary", category: "tv" },
                  { title: "TV - Backup", category: "tv-backup" }
                ] as group}
                  <div class="column">
                    <h3>{group.title}</h3>
                    {#each connections.filter(conn => conn.category === group.category) as connection (connection.address)}
                      {#if connection.show}
                        <!-- svelte-ignore a11y-click-events-have-key-events -->
                        <!-- svelte-ignore a11y-no-static-element-interactions -->
                        <!-- svelte-ignore a11y-missing-attribute -->
                        <a on:click={() => selectConnectionD(connection)}>
                          {connection.name}
                        </a>
                      {/if}
                    {/each}
                  </div>
                {/each}
              </div>
            {/if}
          </div>
          <!-- svelte-ignore a11y-missing-attribute -->
          {#if connectedD}
          <ProfileSelectD />
          <SceneCollectionSelectD />
          <button class="button is-info is-light" disabled>
            {#if heartbeatD && heartbeatD.stats}
              {Math.round(heartbeatD.stats.activeFps)} fps, {Math.round(
                heartbeatD.stats.cpuUsage,
              )}% CPU, {heartbeatD.stats.renderSkippedFrames} skipped frames
            {:else}Connected{/if}
          </button>
            <!-- <button class="button is-info is-light" disabled>{selectedConnectionB.name}</button> -->
            {#if heartbeatD && heartbeatD.streaming && heartbeatD.streaming.outputActive}
              <button
                class="button is-danger"
                on:click={stopStreamD}
                title="Stop Stream"
              >
                <span class="icon"><Icon path={mdiAccessPointOff} /></span>
                <span>{formatTime(heartbeatD.streaming.outputDuration)}</span>
              </button>
            {:else}
              <button
                class="button is-danger is-light"
                on:click={startStreamD}
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
              class:is-light={!isStudioModeD}
              class="button is-link"
              on:click={toggleStudioModeD}
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
            <button
              class="button is-danger is-light"
              on:click={disconnectD}
              title="Disconnect"
            >
              <span class="icon"><Icon path={mdiConnection} /></span>
            </button>
            <button class="button is-link" on:click={() => toggleFullscreensBottomRight('right')}>Full Screen</button>
            <!-- <button class="button is-info is-light" disabled>{connectionStatusB}</button> -->

          {:else}
            <button class="button is-danger" disabled
              >{errorMessageD || "Disconnected"}</button
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
        </div>
        <!-- hoverdisini -->
        <!-- {/if} -->

      </div>
      {#if connectedD}
          {#if isSceneOnTopD}
          <ProgramPreviewD {imageFormatD} />
          {/if}
          <SceneSwitcherD
          bind:scenesD
          buttonStyle={isIconModeD ? "icon" : "text"}
          {editableD}
          />
          {#if !isSceneOnTopD}
          <ProgramPreviewD {imageFormatD} />
          {/if}
          {#each scenesD as scene}
          {#if scene.sceneName.indexOf("(switch)") > 0}
          <SourceSwitcherD
          name={scene.sceneName}
          {imageFormatD}
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
    </section>
    
  </div>
</div>


