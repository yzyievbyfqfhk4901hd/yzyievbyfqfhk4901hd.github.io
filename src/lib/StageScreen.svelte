<script>
  import '../styles/StageScreen.css'
  import { createEventDispatcher } from 'svelte'

  export let fading = false
  const dispatch = createEventDispatcher()

  function handleClick() {
    dispatch('enter')
  }
</script>

<section class="stage-screen {fading ? 'is-fading' : ''}">
  <button class="stage-button" type="button" on:click={handleClick} aria-label="Enter">
    <span class="stage-question">?</span>
  </button>
</section>
