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
    <img class="stage-door" src="/door/door.png" alt="" width="136" height="280" />
  </button>
</section>
