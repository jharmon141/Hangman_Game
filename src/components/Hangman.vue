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
    stroke-miterlimit: 100;
    stroke-width: 8;
  }

  #Face {
    fill: #ffffff;
    stroke: #000000;
    stroke-miterlimit: 100;
    stroke-width: 4;
  }

</style>
