<template>
  <div class="header">
    <button id="btnNewGame" @click="props.restartGame">New Game</button>
    <div class="guessed-letters" v-if="incorrectlyGuessedLetters.length">
      <span>Guessed Letters: </span>
      <span v-for="letter in incorrectlyGuessedLetters" :key="letter">{{ letter.toUpperCase() }}</span>
    </div>
  </div>
  <div class="hangman">
    <svg id="hangman_svg" xmlns="http://www.w3.org/2000/svg" version="1.2" viewBox="0 0 350 633" width="350" height="400">
    <path v-if="strikes > 0 || gameWon" id="Head" fill-rule="evenodd" class="s0" d="m177 193c-50.32 0-91-40.68-91-91 0-50.32 40.68-91 91-91 50.32 0 91 40.68 91 91 0 50.32-40.68 91-91 91z"/>
    <path v-if="strikes > 1 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Body" fill-rule="evenodd" class="s0" d="m175.26 194.53h5l-0.04 272.92h-5z"/>
    <path v-if="strikes > 2 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Left Leg" fill-rule="evenodd" class="s0" d="m173.01 469.43l3.46 3.61-153.44 146.96-3.46-3.61z"/>
    <path v-if="strikes > 3 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Right Leg" fill-rule="evenodd" class="s0" d="m175.62 469.58l3.58-3.49 146.92 150.69-3.58 3.49z"/>
    <path v-if="strikes > 4 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Left Arm" fill-rule="evenodd" class="s0" d="m174.51 334.72l-3.34 3.72-153.51-137.81 3.34-3.72z"/>
    <path v-if="strikes > 5 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Right Arm" fill-rule="evenodd" class="s0" d="m183.73 337.9l-3.42-3.65 144.14-135.28 3.42 3.65z"/>
    <path v-if="strikes > 5" xmlns="http://www.w3.org/2000/svg" id="Face" class="s0" d="m133.84 76.31l3.57-3.5 22.49 22.94-3.58 3.5zm23.04-3.4l3.51 3.56-25.96 25.59-3.51-3.56zm35.22 5.51l3.45-3.62 27.4 26.09-3.45 3.62zm23.64-2.75l3.43 3.65-25.47 23.94-3.42-3.64zm-79.85 59.36l0.08-4.99 81.06 1.34-0.08 5z"/>
    <path v-if="gameWon" xmlns="http://www.w3.org/2000/svg" id="Happy_Face" fill-rule="evenodd" class="s0" d="m112.21 161.49c-16.78-16.97-26.21-39.99-26.21-63.99 0-24 9.43-47.02 26.21-63.99 16.79-16.98 39.55-26.51 63.29-26.51 23.74 0 46.5 9.53 63.29 26.51 16.78 16.97 26.21 39.99 26.21 63.99 0 24-9.43 47.02-26.21 63.99-16.79 16.98-39.55 26.51-63.29 26.51-23.74 0-46.5-9.53-63.29-26.51zm63.29-1.77c26.61 0 49.02-17.68 55.66-41.68 1.36-4.84-2.59-9.23-7.55-9.23h-96.22c-4.96 0-8.88 4.39-7.55 9.23 6.64 24 29.05 41.68 55.66 41.68zm-35.74-76.85c2.1 2.13 4.94 3.32 7.91 3.32 2.97 0 5.81-1.19 7.91-3.32 2.1-2.12 3.28-4.99 3.28-8 0-3-1.18-5.87-3.28-7.99-2.1-2.13-4.94-3.32-7.91-3.32-2.97 0-5.81 1.19-7.91 3.32-2.1 2.12-3.28 4.99-3.28 7.99 0 3.01 1.18 5.88 3.28 8zm55.94-15.99c-2.1 2.12-3.28 4.99-3.28 7.99 0 3.01 1.18 5.88 3.28 8 2.1 2.13 4.94 3.32 7.91 3.32 2.97 0 5.81-1.19 7.91-3.32 2.1-2.12 3.28-4.99 3.28-8 0-3-1.18-5.87-3.28-7.99-2.1-2.13-4.94-3.32-7.91-3.32-2.97 0-5.81 1.19-7.91 3.32z"/>
    </svg>
  </div>
  <div class="game">
    <div class="dashes">
      <span v-for="letter in wordArray" :key="letter">
        <span v-if="correctlyGuessedLetters.includes(letter)">{{ letter.toUpperCase() }}</span>
        <span v-else> _ </span>
      </span>
    </div>
    <div v-if="gameLost" class="lost-game">
      <h2>You Lost! The word was:</h2>
      <h3>{{ props.word.toUpperCase() }}</h3>
    </div>
    <div v-else-if="gameWon" class="won-game">
      <h2>Congrats, You Won!</h2>
    </div>
    <div v-else class="form">
      <h2>Guess a letter:</h2>
      <input v-model="guess" class="input-form" id="input" type="text" maxlength="1" @keyup.enter="submitLetter($event.target.value)" autocomplete="off"/>
      <button @click="submitLetter(guess)" class="input-form" id="submit-button">Submit</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const props = defineProps({
  word: String,
  restartGame: Function,
});

const wordArray = props.word.toLowerCase().split('');
const lettersInWord = ref([...new Set(wordArray)]);

const strikes = ref(0);
const guess = ref('');
const correctlyGuessedLetters = ref([]);
const incorrectlyGuessedLetters = ref([]);

const gameWon = computed(() => {
  const wordSorted = new Array(...lettersInWord.value).sort();
  const correctGuessesSorted = new Array(...correctlyGuessedLetters.value).sort();
  return wordSorted.toString() == correctGuessesSorted.toString();
});

const gameLost = computed(() => {
  return strikes.value > 5;
});

function submitLetter(letter) {
  const guessedLetter = letter.toLowerCase();
  const isCorrectLetter = lettersInWord.value.includes(guessedLetter);
  const isAlreadyGuessed = correctlyGuessedLetters.value.includes(guessedLetter)
    || incorrectlyGuessedLetters.value.includes(guessedLetter);

  //clear the input
  guess.value = '';

  //if not a letter or already guessed return and do nothing
  if (guessedLetter.match(/[^a-z]/g) || !guessedLetter.length) {
    return;
  }

  if (isAlreadyGuessed) {
    alert("You've already guessed that letter")
    return;
  } else if (isCorrectLetter) {
    correctlyGuessedLetters.value.push(guessedLetter)
  } else {
    incorrectlyGuessedLetters.value.push(guessedLetter)
    strikes.value++;
  }

  if (gameLost.value || gameWon.value) {
    document.getElementById('btnNewGame').focus();
  }

  //confetti blast
  if (gameWon.value) {
    window.confetti({
      particleCount: 250 
    });
  }
}

onMounted(() => {
  document.getElementById('input').focus();
})
</script>

<style scoped>
  .header {
    margin-top: 10px;
    display: flex;
    justify-content: space-between;
  }

  .header button {
    display: inline-flex;
  }

  .guessed-letters {
    display: inline;
  }

  .hangman {
    display: flex;
    justify-content: center;
  }

  .game {
    text-align: center;
    margin-top: 25px;
  }

  .dashes {
    font-size: 36px;
  }

  .input-form {
    font-size: 24px;
  }

  #input {
    font-size: 18px;
    text-align: center;
  }

  #submit-button {
    font-size: 18px;
  }

  #hangman_svg {
    margin-top: 50px;
  }

  .s0 {
    fill: none;
    stroke: #000000;
    stroke-width: 8;
    animation: draw 2s forwards;
    stroke-dasharray: 571;
    stroke-dashoffset: 571;
  }

  @keyframes draw {
  to {
    stroke-dashoffset: 0;
  }
}

  #Face {
    fill: #ffffff;
    stroke: #000000;
    stroke-miterlimit: 100;
    stroke-width: 4;
  }
</style>
