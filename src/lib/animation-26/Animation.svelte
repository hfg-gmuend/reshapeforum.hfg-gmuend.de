<script>
  import { onMount, onDestroy } from "svelte";
  import Blob from './Blob.svelte';

  let width = 1000;
  let bannerEl;
  let blobY = 0;
  const offsetY = -280; // 🎯 Wieviel höher als das Banner die Blobs sitzen

  function updateWidth() {
    width = window.innerWidth * 0.4;
    document.documentElement.style.setProperty('--blob-width', `${width}px`);
  }

  function updateBlobPosition() {
    if (bannerEl) {
      const rect = bannerEl.getBoundingClientRect();
      blobY = rect.top + rect.height / 2 + offsetY;
    }
  }

  onMount(() => {
    updateWidth();
    updateBlobPosition();
    window.addEventListener("resize", () => {
      updateWidth();
      updateBlobPosition();
    });

    onDestroy(() => {
      window.removeEventListener("resize", updateWidth);
    });
  });
</script>

<style>
  .fullscreen-center {
    position: relative;
    width: 100%;
    height: 100vh;
    overflow: hidden;
  }

  .banner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 25%;
    max-width: 80vw;
    z-index: 3;
  }

  .blob-container {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    z-index: 1;
    pointer-events: none;
  }

  .blob-left,
  .blob-right {
    position: absolute;
    width: 70%;
    height: 120vh;
    pointer-events: none;
  }

  .blob-left {
    left: 25%;
  }

  .blob-right {
    right: 25%;
  }

  @media (max-width: 768px) {
    .banner {
      width: 40%;
    }

    .blob-left,
    .blob-right {
      width: 90%;
      height: 90vh;
    }
  }

  @media (max-width: 480px) {
    .banner {
      width: 50%;
    }

    .blob-left,
    .blob-right {
      width: 100%;
      height: 70vh;
    }
  }
</style>

<div class="fullscreen-center">
  <img src="banner-font.png" alt="Banner" class="banner" bind:this={bannerEl} />

  <div class="blob-container">
    <div class="blob-left" style="top: {blobY}px;">
      <Blob mirrorY={true} />
    </div>
    <div class="blob-right" style="top: {blobY}px;">
      <Blob mirrorY={false} />
    </div>
  </div>
</div>
