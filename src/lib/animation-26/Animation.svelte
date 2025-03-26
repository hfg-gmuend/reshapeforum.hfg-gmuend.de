<script>
  import { onMount, onDestroy } from "svelte";
  import Blob from './Blob.svelte';

  let width = 1000; // Standardwert für die Breite

  // Funktion zur Berechnung der Breite basierend auf der Fenstergröße
  function updateWidth() {
    width = window.innerWidth * 0.4; // 40% der Fensterbreite
    document.documentElement.style.setProperty('--blob-width', `${width}px`);
  }

  onMount(() => {
    updateWidth();
    window.addEventListener("resize", updateWidth);
    onDestroy(() => window.removeEventListener("resize", updateWidth));
  });
</script>

<style>
  .fullscreen-center {
    position: relative;
    width: 100%;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    pointer-events: auto;
  }

  .banner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 25%;
    max-width: 80vw;
    z-index: 3; /* Banner sollte vor den Blobs sein */
  }

  .blob-container {
    position: absolute;
    top: 50%;
    left: 48%;
    transform: translate(-50%, -50%);
    width: 100%;
    height: 100%; /* Blobs füllen den Container aus */
    z-index: 2; /* Blobs sind hinter dem Banner */
  }

  .blob-left, .blob-right {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 50%;

  }

  .blob-left {
    left: 10%; /* Linke Blob-Position anpassen */
    z-index: 1; /* Blobs sind hinter dem Banner */

  }

  .blob-right {
    left: 36%; /* Rechte Blob-Position anpassen */
    z-index: 0; /* Blobs sind hinter dem Banner */

  }

  @media (max-width: 768px) {
    .blob-left, .blob-right {
      width: 35%; /* Kleinere Blobs für kleinere Bildschirme */
      height: 45vh;
    }
  }

  @media (max-width: 480px) {
    .blob-left, .blob-right {
      width: 50%; /* Noch kleinere Blobs für sehr kleine Bildschirme */
      height: 40vh;
    }
  }
</style>


<div class="fullscreen-center">
  <img src="banner-font.png" alt="Banner" class="banner" />

  <!-- Blob-Container hinter dem Banner -->
  <div class="blob-container">
    <!-- Linke Animation (Blob) -->
    <div class="blob-left">
      <Blob class="blob" mirrorY={true} {width} />
    </div>

    <!-- Rechte Animation (Blob) -->
    <div class="blob-right">
      <Blob class="blob" mirrorY={false} {width} />
    </div>
  </div>
</div>