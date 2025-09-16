<script lang="ts">
  import Fa from 'svelte-fa';
  import { faDiceOne, faDiceTwo, faDiceThree, faDiceFour, faDiceFive, faDiceSix } from '@fortawesome/free-solid-svg-icons';

  // Access props with live updates
  const { onAddStep } = $props<{ onAddStep: () => void }>();

  const DIFFICULTY_LEVELS = {
    SUPER_HARD: 0,
    HARD: 1,
    NORMAL: 2,
    EASY: 3,
    SUPER_EASY: 4
  } as const;

  type DifficultyLevel = typeof DIFFICULTY_LEVELS[keyof typeof DIFFICULTY_LEVELS];

  const difficulties = [
    { level: DIFFICULTY_LEVELS.SUPER_HARD, label: "Veoma teško", diceCount: 3 },
    { level: DIFFICULTY_LEVELS.HARD, label: "Teško", diceCount: 2 },
    { level: DIFFICULTY_LEVELS.NORMAL, label: "Normalno", diceCount: 1 },
    { level: DIFFICULTY_LEVELS.EASY, label: "Lako", diceCount: 2 },
    { level: DIFFICULTY_LEVELS.SUPER_EASY, label: "Veoma lako", diceCount: 3 }
  ];

  const diceIcons = [faDiceOne, faDiceTwo, faDiceThree, faDiceFour, faDiceFive, faDiceSix];

  let selectedDifficulty = $state<DifficultyLevel | null>(null);
  let tried = $state(false);
  let diceRolls = $state<number[]>([]);
  let words = $state<string[]>([]);
  let outcome = $state<{ text: string; color: string; style: string } | null>(null);

  const outcomes = [
    { text: "Ne, i ...", color: "#c9302c", style: "bold" },
    { text: "Ne", color: "#e4716e", style: "normal" },
    { text: "Ne, ali ...", color: "#e8834f", style: "italic" },
    { text: "Da, ali...", color: "#a3be63", style: "italic" },
    { text: "Da", color: "#79c779", style: "normal" },
    { text: "Da, i ...", color: "#449d44", style: "bold" }
  ];

  function rollDie() {
    return Math.floor(Math.random() * 6) + 1;
  }

  function getOutcome(difficulty: DifficultyLevel, rolls: number[]): { text: string; color: string; style: string } | null {
    if (rolls.length === 0) return null;
    
    // For normal, easy, super easy - use max value
    // For hard, super hard - use min value
    const isEasyDifficulty = difficulty >= DIFFICULTY_LEVELS.NORMAL;
    const targetValue = isEasyDifficulty ? Math.max(...rolls) : Math.min(...rolls);
    
    // Map 1-6 to outcomes array (0-5 index)
    return outcomes[targetValue - 1];
  }

  function handleTryClick() {
    if (selectedDifficulty === null) return;
    const selectedDifficultyConfig = difficulties.find(d => d.level === selectedDifficulty)!;
    const numDice = selectedDifficultyConfig.diceCount;
    diceRolls.splice(0, diceRolls.length, ...Array.from({ length: numDice }, rollDie));
    outcome = getOutcome(selectedDifficulty, diceRolls);
    words.splice(0, words.length,
      Math.floor(Math.random() * 1000).toString(),
      Math.floor(Math.random() * 1000).toString(),
      Math.floor(Math.random() * 1000).toString()
    );
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
            value={difficulty.level}
            checked={selectedDifficulty === difficulty.level}
            onchange={() => selectedDifficulty = difficulty.level}
          />
          <span class="radio-label">{difficulty.label}</span>
        </label>
      {/each}
    </fieldset>

    <button onclick={handleTryClick} disabled={selectedDifficulty === null}>
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

    {#if outcome}
      <div class="outcome" style="color: {outcome.color}; border-color: {outcome.color}; background-color: {outcome.color}1a;">
        {#if outcome.style === 'bold'}
          <strong>{outcome.text}</strong>
        {:else if outcome.style === 'italic'}
          <em>{outcome.text}</em>
        {:else}
          {outcome.text}
        {/if}
      </div>
    {/if}

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

  .outcome {
    font-size: 1.8rem;
    font-weight: bold;
    text-align: center;
    padding: 1rem;
    border-radius: 8px;
    border: 2px solid;
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