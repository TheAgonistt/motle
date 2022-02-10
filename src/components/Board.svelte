<script>
  import { onMount } from 'svelte';
  const alphabet = [...Array(26)].map((_, i) => String.fromCharCode(65 + i).toLowerCase());

  // props
  export let currentStep = 0;
  export let settings = {};
  export let grid = [];
  let guess = [];

  // handle each inputs
  const handleGuessLetter = (event, field, rowIndex, columnIndex) => {
    const value = event.target.value;
    const isValidLetter = alphabet.includes(value);

    if (!isValidLetter) {
      field.ref.value = '';
      return false;
    }

    guess[columnIndex] = value ? value.toLowerCase() : '?';

    // focus the next input
    const isLastInput = columnIndex === wordLength - 1;
    if (!isLastInput) {
      // check is the next input's state
      const nextInputState = grid[rowIndex].state;
      grid[rowIndex]['fields'][columnIndex + 1].ref.focus();
    }
  }

  // handle form submit
  const handleSubmit = () => {
    validateGuess();
  }

  const handleGlobalSubmit = () => {
    // click on the real button lol
    const button = grid[currentStep]['button'];
    if (button) {
      button.click();
    }
    
  }

  const isValidGuess = () => {
    const hasEmptyLetter = guess.some((letter) => letter === '?');
    if ((guess.length < wordLength - 1) || hasEmptyLetter) {
      return false;
    }

    return true;
  }

  const validateGuess = () => {
    
    if (isValidGuess()) {
      const validation = guess.map((letter, index) => {
        const letterIsPresent = settings.wordle.includes(letter);
        let letterIndexMatch = false;
        if (letterIsPresent) {
          const wordleLetter = settings.wordle[index];
          letterIndexMatch = wordleLetter === letter;
        }

        const statusType = () => {
          if (letterIndexMatch) {
            return 'egzakt';
          }

          if (letterIsPresent) {
            return 'partial'
          }

          return 'invalid'
        }

        return {
          letter: letter,
          status: statusType(),
        }
      });

      updateRow(validation);
      nextRow();
    }
  }

  const updateRow = (validation) => {
    const activeRow =  grid[currentStep];
    const activeFields =  activeRow.fields;

    // check first for all matched
    const purfect = validation.every((item) => {
      return item.status === 'egzakt';
    });

    if (purfect) {
      winGame();
    }

    // check for all wrong
    const allWrong = validation.every((item) => {
      return item.status === 'invalid';
    });

    if (allWrong) {
      console.log('!!!!!!!!!!! allWrong !!!!!!!!!!!!!');
    }

    // all other cases (mix of egzaktm partial and invalid)

    // update fields
    const newArrayOfFields = activeFields.map((field, index) => {
      const currentValidationState = validation[index].status;
      const newState = currentValidationState;
      return {...field, state: newState};
    });

    activeRow.fields = [...newArrayOfFields];
    activeRow.state = false;
    grid[currentStep] = grid[currentStep];
  }

  const winGame = () => {
    console.log('WINNNNEERRR!!!!!!');
  }

  const nextRow = () => {
    grid[currentStep].state = 'inactive';
    guess = [];

    if (currentStep !== rowLength) {
      currentStep++
      grid[currentStep].state = 'active';
      grid[currentStep].fields[0].ref.focus();
    }
  }

  // helpers
  const rowLength = grid?.length || 0;
  const wordLength = grid[0]?.fields?.length || 0;

  const generateFormStateClass = (state) => {
    switch (state) {
      case 'active':
        return 'is-active';
      case 'inactive':
        return 'is-inactive';
      default:
        return ''
    }
  }

  const generateInputStateClass = (state) => {
    switch (state) {
      case 'egzakt':
        return 'is-egzakt';
      case 'partial':
        return 'is-partial';
      case 'invalid':
        return 'is-invalid';
      default:
        return ''
    }
  }

  onMount(() => {
    // active the first row and focus the first field
    grid[0].state = 'active';
	});
  
  console.log('grid:', grid);
  console.log('settings:', settings);
</script>

<div class="c-Board">
  <div class="container">
    {#if grid.length > 0}
    <div class="c-Board__list">
      {#each grid as row, rowIndex}
        <form
          class={`c-Board__list--row ${generateFormStateClass(row.state)}`}
          on:submit|preventDefault={handleSubmit}
          bind:this={row['form']}
        >
          {#each row.fields as field, columnIndex}
            <div class="c-Board__list--column">
              <div
                class={`c-Board__item ${generateInputStateClass(field.state)}`}
              >
                <input
                  type="text" 
                  maxlength="1"
                  on:input="{(event) => {
                    handleGuessLetter(event, field, rowIndex, columnIndex)
                  }}"
                  bind:this={field['ref']}
                >
              </div>
            </div>
          {/each}

          <button
            class="c-Board__list--button"
            type="submit"
            bind:this={grid[rowIndex]['button']}
          >submit!</button>
        </form>
      {/each}
    </div>
    {/if}

    <div class="c-Board--submit__wrapper">
      <button
        class="c-Board--submit"
        disabled={guess.length < 5}
        type="submit"
        on:click={handleGlobalSubmit}
      >Guess</button>
    </div>
  </div>
</div>

<style>
  .c-Board--submit__wrapper {
    display: flex;
    justify-content: center;
    padding-block: 40px;
  }

  .c-Board--submit {
    margin: 0;
    padding: 0.8em 2em;
    background: transparent;
    font-size: 1.2rem;
    font-weight: bold;
    color: var(--primary-lighter);
    border: 2px solid var(--primary-lighter);
    text-transform: uppercase;
    opacity: 1;
    transition: all ease-in .15s;
  }

  .c-Board--submit[disabled] {
    cursor: not-allowed;
    opacity: 0.5;
  }

	.c-Board__list--row {
		display: flex;
    justify-content: space-between;
    flex-wrap: nowrap;
    opacity: 1;
    transition: all ease-in .3s;
	}

  .c-Board__list--row.is-inactive {
    pointer-events: none!important;
    opacity: 0.5;
  }

  .c-Board__list--column {
    flex: 1 1 20%;
    padding: 5px;
  }

  .c-Board__list--button:not(:focus):not(:active) {
    clip: rect(0 0 0 0); 
    clip-path: inset(50%);
    height: 1px;
    overflow: hidden;
    position: absolute;
    white-space: nowrap; 
    width: 1px;
  }

  .c-Board__item {
    position: relative;
    padding-top: 100%;
    width: 100%;
    border: 0;
    outline: 2px solid var(--secondary);
  }

  .c-Board__item.is-egzakt {
    background: #388e3c;
  }

  .c-Board__item.is-partial {
    background: #f57c00;
  }

  .c-Board__item.is-invalid {
    background: var(--secondary);
  }

  .c-Board__item input {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    background: transparent;
    border: 0;
    font-size: 2rem;
    color: var(--primary-lighter);
    text-align: center;
    text-transform: uppercase;
  }
</style>