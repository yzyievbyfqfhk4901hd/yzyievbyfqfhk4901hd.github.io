<script>
  import CatScreen from './lib/CatScreen.svelte'
  import StageScreen from './lib/StageScreen.svelte'
  import { onMount } from 'svelte'

  const fadeMs = 700

  let audioEl
  let doorAudioEl
  let showStage = true
  let showCat = false
  let isBlack = false
  let isFading = false
  let allowPhrases = false

  onMount(() => {
    document.title = 'suspicious-looking door'
  })

  function wait(ms) {
    return new Promise((resolve) => {
      setTimeout(resolve, ms)
    })
  }

  function playDoorAndWait() {
    if (!doorAudioEl) return Promise.resolve()
    doorAudioEl.currentTime = 0

    return new Promise((resolve) => {
      let done = false

      const finish = () => {
        if (done) return
        done = true
        doorAudioEl.removeEventListener('ended', finish)
        resolve()
      }

      doorAudioEl.addEventListener('ended', finish, { once: true })

      const playPromise = doorAudioEl.play()
      if (playPromise?.catch) {
        playPromise.catch(() => finish())
      }

      if (Number.isFinite(doorAudioEl.duration) && doorAudioEl.duration > 0) {
        const timeoutMs = Math.ceil(doorAudioEl.duration * 1000) + 50
        setTimeout(finish, timeoutMs)
      }
    })
  }

  async function handleStageClick() {
    if (isFading || showCat) return
    isFading = true
    isBlack = true

    const doorPromise = playDoorAndWait()
    await wait(fadeMs)
    showStage = false
    await doorPromise

    if (audioEl) {
      audioEl.currentTime = 0
      audioEl.play().catch(() => {})
    }

    showCat = true
    isBlack = false
    document.title = 'suspicious-looking cat'
    await wait(fadeMs)
    allowPhrases = true
  }
</script>

<div class="app">
  <audio bind:this={audioEl} src="/music/cheshire.mp3" preload="auto" loop></audio>
  <audio bind:this={doorAudioEl} src="/sfx/door-open.mp3" preload="auto"></audio>
  <div class="black-overlay {isBlack ? 'is-black' : ''}"></div>
  {#if showStage}
    <StageScreen fading={isFading} on:enter={handleStageClick} />
  {/if}
  {#if showCat}
    <CatScreen showPhrases={allowPhrases} />
  {/if}
</div>
