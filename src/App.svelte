<script lang="ts">
  import Step from './lib/Step.svelte';

  let steps = $state([{ id: 1 }]);

  function addStep() {
    steps.push({ id: steps.length + 1 });
    // Scroll after microtask so DOM has appended node
    queueMicrotask(() => {
      window.scrollTo({
        top: document.body.scrollHeight,
        behavior: 'smooth'
      });
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