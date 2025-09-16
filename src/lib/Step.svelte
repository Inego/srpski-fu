<script lang="ts">
  // The callback prop for Svelte 5 best practices
  export let onAddStep: () => void;

  // Data for our UI elements
  const difficulties = ["Tough", "Difficult", "Normal", "Easy", "Super easy"];
  
  // Using Font Awesome dice icons via svelte-fa
  import Fa from 'svelte-fa';
  import { faDiceOne, faDiceTwo, faDiceThree, faDiceFour, faDiceFive, faDiceSix } from '@fortawesome/free-solid-svg-icons';
  const diceIcons = [faDiceOne, faDiceTwo, faDiceThree, faDiceFour, faDiceFive, faDiceSix];

  let words: string[] = [];
  let diceRolls: number[] = [];
  let selectedDifficulty: string | null = null;
  let tried = false;

  const diceCountMap: { [key: string]: number } = {
    "Tough": 3,
    "Difficult": 2,
    "Normal": 1,
    "Easy": 2,
    "Super easy": 3
  };

  function handleTryClick() {
    if (!selectedDifficulty) return;
    
    const numDice = diceCountMap[selectedDifficulty];
    const newRolls: number[] = [];
    for (let i = 0; i < numDice; i++) {
      const roll = Math.floor(Math.random() * 6) + 1;
      newRolls.push(roll);
    }
    diceRolls = newRolls;

    words = [
      Math.floor(Math.random() * 1000).toString(),
      Math.floor(Math.random() * 1000).toString(),
      Math.floor(Math.random() * 1000).toString()
    ];

    onAddStep();
    tried = true;
  }
</script>

<div class="step-container">
  <!-- Show the controls initially -->
  {#if !tried}
    <fieldset class="difficulty-selector">
      {#each difficulties as difficulty}
        <label class="radio-option">
          <input
            type="radio"
            name="difficulty"
            value={difficulty}
            bind:group={selectedDifficulty}
          />
          <span class="radio-label">{difficulty}</span>
        </label>
      {/each}
    </fieldset>

    <button on:click={handleTryClick} disabled={!selectedDifficulty}>
      Try
    </button>

  <!-- After 'Try' is clicked, show the dice and words instead -->
  {:else}
    <div class="dice-results">
      <!-- CHANGED: Look up the character in the array using the roll -->
      <!-- Since arrays are 0-indexed, we use `roll - 1` -->
      {#each diceRolls as roll}
        <span class="dice">
          <Fa icon={diceIcons[roll - 1]} class="dice-icon" />
        </span>
      {/each}
    </div>

    <ul>
      {#each words as word}
        <li>{word}</li>
      {/each}
    </ul>
  {/if}
</div>

<style>
  .step-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    /* REMOVED: This property interferes with the container's ability to grow with its content. */
    /* justify-content: flex-start; */
    gap: 2rem;
    padding: 1rem 2rem;
    box-sizing: border-box;
    border-bottom: 1px solid #eee;
    min-height: 150px;
  }

  /* ... rest of the styles are unchanged ... */
  .difficulty-selector {
    display: inline-block;
    border: none;
    padding: 0;
    margin: 0;
    text-align: center;
    white-space: normal;
  }

  .radio-option {
    display: inline-flex;
    align-items: center;
    gap: 0.25rem;
    margin-right: 0.35rem;
    vertical-align: middle;
    cursor: pointer;
  }

  .radio-option:last-child {
    margin-right: 0;
  }

  .radio-label {
    font-size: 0.95rem;
  }

  button {
    padding: 0.75rem 2rem;
    font-size: 1rem;
    border-radius: 8px;
    border: 1px solid transparent;
    cursor: pointer;
    transition: border-color 0.25s;
  }
  button:hover {
    border-color: #646cff;
  }
  
  .dice-results {
    display: flex;
    gap: 1rem;
  }

  .dice {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 80px;
    height: 80px;
    border: none;
    border-radius: 8px;
    font-size: 3.6rem; /* doubled font size to scale with the dice */
    font-weight: bold;
    color: #fff;
  }
  
  @media (prefers-color-scheme: light) {
    .dice {
      color: #213547;
    }
  }

  ul {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    gap: 1rem;
    font-size: 1.5em;
    color: #aaa;
  }
</style>