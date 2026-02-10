<script>
  import CatScreen from './lib/CatScreen.svelte'
  import StageScreen from './lib/StageScreen.svelte'
  import { onMount } from 'svelte'

  const fadeMs = 700
  const blackHoldMs = 1200

  let audioEl
  let showStage = true
  let showCat = false
  let isBlack = false
  let isFading = false
  let allowPhrases = false

  onMount(() => {
    document.title = 'suspicious-looking place'
  })

  function handleStageClick() {
    if (isFading || showCat) return
    isFading = true
    isBlack = true
    setTimeout(() => {
      showStage = false
      if (audioEl) {
        audioEl.currentTime = 0
        audioEl.play().catch(() => {})
      }
    }, fadeMs)
    setTimeout(() => {
      showCat = true
      isBlack = false
      document.title = 'suspicious-looking cat'
    }, fadeMs + blackHoldMs)
    setTimeout(() => {
      allowPhrases = true
    }, fadeMs + blackHoldMs + fadeMs)
  }
</script>

<div class="app">
  <audio bind:this={audioEl} src="/music/cheshire.mp3" preload="auto" loop></audio>
  <div class="black-overlay {isBlack ? 'is-black' : ''}"></div>
  {#if showStage}
    <StageScreen fading={isFading} on:enter={handleStageClick} />
  {/if}
  {#if showCat}
    <CatScreen showPhrases={allowPhrases} />
  {/if}
</div>
