<script>
  import '../styles/CatScreen.css'
  import { onMount } from 'svelte'
  import phrasesData from '../data/phrases.json'

  export let showPhrases = true

  let displayText = ''
  let running = false
  let cleanupLoop = null
  const typingSpeedMs = 70
  const deletingSpeedMs = 40
  const holdMs = 6000
  const gapMs = 300

  const phrases = phrasesData?.phrases ?? []
  const introPhrase = phrasesData?.intro ?? ''

  function pickPhrase(previous) {
    if (!phrases.length) return ''
    if (phrases.length === 1) return phrases[0]
    let next = phrases[Math.floor(Math.random() * phrases.length)]
    while (next === previous) {
      next = phrases[Math.floor(Math.random() * phrases.length)]
    }
    return next
  }

  function wait(ms, timeouts) {
    return new Promise((resolve) => {
      const id = setTimeout(() => {
        timeouts.delete(id)
        resolve()
      }, ms)
      timeouts.add(id)
    })
  }

  function startLoop() {
    const timeouts = new Set()
    running = true
    let isFirst = true

    const loop = async () => {
      while (running) {
        const phrase = isFirst && introPhrase ? introPhrase : pickPhrase(displayText || null)
        isFirst = false
        displayText = ''
        for (let i = 0; i < phrase.length && running; i += 1) {
          displayText = phrase.slice(0, i + 1)
          await wait(typingSpeedMs, timeouts)
        }
        if (!running) break
        await wait(holdMs, timeouts)
        if (!running) break
        for (let i = phrase.length; i >= 0 && running; i -= 1) {
          displayText = phrase.slice(0, i)
          await wait(deletingSpeedMs, timeouts)
        }
        await wait(gapMs, timeouts)
      }
    }

    loop()

    return () => {
      running = false
      for (const id of timeouts) clearTimeout(id)
      timeouts.clear()
    }
  }

  function stopLoop() {
    if (cleanupLoop) cleanupLoop()
    cleanupLoop = null
    displayText = ''
  }

  $: {
    if (showPhrases) {
      if (!cleanupLoop) cleanupLoop = startLoop()
    } else {
      stopLoop()
    }
  }

  onMount(() => {
    if (showPhrases && !cleanupLoop) cleanupLoop = startLoop()
    return () => stopLoop()
  })
</script>

<section class="cat-screen" aria-label="Cat">
  <div class="cat-stack">
    <div
      class="cat-phrase {showPhrases ? '' : 'is-hidden'}"
      aria-live="polite"
    >
      {displayText}
    </div>
    <img class="cat-image" src="/entry/cheshire.gif" alt="cheshire" />
  </div>
</section>
