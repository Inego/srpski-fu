<!-- src/App.svelte -->

<script lang="ts">
  import { tick } from 'svelte';
  import Step from './lib/Step.svelte';

  let steps = [{ id: 1 }];

  async function addStep() {
    // 1. Update the state
    steps = [...steps, { id: steps.length + 1 }];

    // 2. Wait for Svelte to update the DOM
    await tick();

    // 3. Now that the new Step is rendered, scroll to the bottom of the page
    window.scrollTo({
      top: document.body.scrollHeight,
      behavior: 'smooth' // for a nice, smooth scrolling effect
    });
  }
</script>

<main>
  {#each steps as step (step.id)}
    <Step onAddStep={addStep} />
  {/each}
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    
    padding: 2rem;
    box-sizing: border-box;
    gap: 2rem;
  }
</style>