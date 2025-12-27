<template>
  <div class="header">
    <button id="btnNewGame" @click="props.restartGame">New Game</button>
    <div class="guessed-letters" v-if="incorrectlyGuessedLetters.length && !isMobile">
      <span>Guessed Letters: </span>
      <span v-for="letter in incorrectlyGuessedLetters" :key="letter">{{ letter.toUpperCase() }}</span>
    </div>
  </div>
  <div class="hangman">
    <svg id="hangman_svg" xmlns="http://www.w3.org/2000/svg" version="1.2" viewBox="0 0 350 633" width="350" height="400">
    <path v-if="strikes > 0 || gameWon" id="Head" fill-rule="evenodd" class="s0" d="m177 193c-50.32 0-91-40.68-91-91 0-50.32 40.68-91 91-91 50.32 0 91 40.68 91 91 0 50.32-40.68 91-91 91z"/>
    <line v-if="strikes > 1 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Body" fill-rule="evenodd" class="s0" x1="175" y1="194.53" x2="175" y2="470"/>
    <line v-if="strikes > 2 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Left Leg" fill-rule="evenodd" class="s0" x1="175" y1="470" x2="-100" y2="800"/>
    <line v-if="strikes > 3 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Right Leg" fill-rule="evenodd" class="s0" x1="175" y1="470" x2="450" y2="800"/>
    <line v-if="strikes > 4 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Left Arm" fill-rule="evenodd" class="s0" x1="175" y1="325" x2="25" y2="225"/>
    <line v-if="strikes > 5 || gameWon" xmlns="http://www.w3.org/2000/svg" id="Right Arm" fill-rule="evenodd" class="s0" x1="175" y1="325" x2="325" y2="225"/>
    <path v-if="strikes > 5" xmlns="http://www.w3.org/2000/svg" id="Face" class="s0" d="m133.84 76.31l3.57-3.5 22.49 22.94-3.58 3.5zm23.04-3.4l3.51 3.56-25.96 25.59-3.51-3.56zm35.22 5.51l3.45-3.62 27.4 26.09-3.45 3.62zm23.64-2.75l3.43 3.65-25.47 23.94-3.42-3.64zm-79.85 59.36l0.08-4.99 81.06 1.34-0.08 5z"/>
    <path v-if="gameWon" xmlns="http://www.w3.org/2000/svg" id="Happy_Face" fill-rule="evenodd" d="m175.25 105.04c42.06-0.03 50.29 0.19 53.25 1.46 2.22 0.95 4.14 2.8 5.25 5.06 1.53 3.12 1.59 4.13 0.48 8.25-0.71 2.58-2.67 7.39-4.36 10.69-1.7 3.3-5.18 8.43-7.73 11.41-2.55 2.97-7.34 7.31-10.64 9.64-3.3 2.33-8.7 5.36-12 6.74-3.3 1.37-8.47 2.97-11.5 3.56-3.03 0.58-7.97 1.35-11 1.69-3.96 0.45-7.74 0.06-13.5-1.41-4.4-1.12-10.25-3.01-13-4.21-2.75-1.2-7.7-4.07-11-6.39-3.3-2.32-8.09-6.65-10.64-9.62-2.55-2.98-6.03-8.11-7.73-11.41-1.69-3.3-3.65-8.11-4.36-10.69-1.11-4.12-1.05-5.13 0.48-8.25 1.04-2.11 3.08-4.16 5-5.02 2.71-1.22 11.58-1.47 53-1.5zm-49.76 17.76c1.65 3.68 4.8 9.11 7 12.06 2.21 2.95 6.94 7.54 10.51 10.21 3.57 2.67 8.97 5.8 12 6.94 3.03 1.14 8.65 2.56 12.5 3.14 3.85 0.58 9.93 0.74 13.5 0.36 3.57-0.38 9.2-1.58 12.5-2.66 3.3-1.09 8.47-3.61 11.5-5.6 3.03-1.99 7.72-5.89 10.43-8.68 3.21-3.3 6.31-8.04 8.88-13.57 2.88-6.2 3.68-8.91 2.96-10-0.85-1.29-7.9-1.54-51.51-1.76-44.68-0.23-50.68-0.08-51.89 1.3-1.17 1.33-0.94 2.51 1.62 8.26zm22.26-62.8c1.79 0 4.49 0.63 6 1.4 1.51 0.78 3.87 2.62 5.25 4.09 1.94 2.09 2.57 3.96 2.81 8.35 0.23 4.36-0.17 6.41-1.75 8.91-1.14 1.79-3.28 4.04-4.77 5-1.7 1.1-4.57 1.73-7.75 1.7-3.28-0.03-6.04-0.72-7.92-2-1.59-1.07-3.78-3.75-4.88-5.95-1.47-2.97-1.84-5.16-1.42-8.5 0.31-2.48 1.41-5.63 2.44-7 1.04-1.38 3.43-3.29 5.31-4.25 1.89-0.96 4.89-1.75 6.68-1.75zm-6.5 11.5c-0.69 1.38-1.27 3.06-1.28 3.75-0.02 0.69 0.88 2.49 2 4 1.54 2.09 2.87 2.75 5.53 2.75 2.17 0 4.26-0.76 5.5-2 1.1-1.1 2.02-3.24 2.04-4.75 0.03-1.51-0.76-3.74-1.75-4.96-0.98-1.21-2.92-2.43-4.29-2.7-1.38-0.27-3.4-0.07-4.5 0.46-1.1 0.52-2.56 2.08-3.25 3.45zm62-11.5c1.51 0 4.21 0.62 6 1.37 1.79 0.76 4.37 2.6 5.75 4.1 1.7 1.86 2.63 4.21 2.91 7.38 0.23 2.67-0.19 6.1-1 8.06-0.78 1.87-2.54 4.37-3.91 5.55-1.38 1.17-4.19 2.45-6.25 2.84-2.06 0.38-5.55 0.25-7.75-0.3-2.79-0.7-4.83-2.11-6.75-4.69-1.95-2.62-2.85-5.15-3.09-8.75-0.27-4.03 0.14-5.77 2.02-8.56 1.3-1.93 3.93-4.29 5.84-5.25 1.91-0.96 4.72-1.75 6.23-1.75zm-5.97 10.93c-0.67 0.86-1.25 2.47-1.28 3.57-0.04 1.1 0.85 3.24 1.97 4.75 1.54 2.09 2.87 2.75 5.53 2.75 2.36 0 4.16-0.73 5.52-2.25 1.11-1.24 2.03-3.38 2.04-4.75 0.01-1.38-0.78-3.49-1.77-4.71-0.98-1.21-2.92-2.45-4.29-2.74-1.38-0.3-3.4-0.01-4.5 0.63-1.1 0.65-2.55 1.88-3.22 2.75z"/>
    </svg>
  </div>
  <div class="game">
    <div class="dashes">
      <span v-for="letter in wordArray" :key="letter">
        <span v-if="correctlyGuessedLetters.includes(letter)">{{ letter.toUpperCase() }}</span>
        <span v-else> _ </span>
      </span>
    </div>
    <div v-if="gameLost" class="fade-in">
      <h2>You <span class="red"> Lost</span>! The word was:</h2>
      <h3>{{ props.word.toUpperCase() }}</h3>
    </div>
    <div v-else-if="gameWon" class="fade-in">
      <h2>Congrats, You <span class="green">Won</span>!</h2>
    </div>
    <div v-else class="form">
      <div v-if="isMobile">
        <h3>Guess a letter:</h3>
        <div class="guessSubmit">
          <span id="guessValue">{{ guess.toUpperCase() }}</span>
          <button @click="submitLetter(guess)" id="submit-button-mobile">Submit</button>
        </div>
        <div class="letterRow Top">
          <span v-for="letter in lettersTopRow" :key="letter">
            <span v-if="isGuessedLetter(letter)" class="letters guessed">{{ letter.toUpperCase() }}</span>
            <span v-else-if="isCurrentGuess(letter)" class="letters currentGuess">{{ letter.toUpperCase() }}</span>
            <span v-else class="letters" @click="setGuess(letter)">{{ letter.toUpperCase() }}</span>
          </span>
        </div>
        <div class="letterRow Middle">
          <span v-for="letter in lettersMiddleRow" :key="letter">
            <span v-if="isGuessedLetter(letter)" class="letters guessed">{{ letter.toUpperCase() }}</span>
            <span v-else-if="isCurrentGuess(letter)" class="letters currentGuess">{{ letter.toUpperCase() }}</span>
            <span v-else class="letters" @click="setGuess(letter)">{{ letter.toUpperCase() }}</span>
          </span>
        </div>
        <div class="letterRow Bottom">
          <span v-for="letter in lettersBottomRow" :key="letter">
            <span v-if="isGuessedLetter(letter)" class="letters guessed">{{ letter.toUpperCase() }}</span>
            <span v-else-if="isCurrentGuess(letter)" class="letters currentGuess">{{ letter.toUpperCase() }}</span>
            <span v-else class="letters" @click="setGuess(letter)">{{ letter.toUpperCase() }}</span>
          </span>
        </div>
      </div>
      <div v-else>
        <h2>Guess a letter:</h2>
        <input v-model="guess" class="input-form" id="input" type="text" maxlength="1" @keyup.enter="submitLetter($event.target.value)" autocomplete="off"/>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const props = defineProps({
  word: String,
  restartGame: Function,
  isMobile: Boolean,
});

const wordArray = props.word.toLowerCase().split('');
const lettersInWord = ref([...new Set(wordArray)]);
const lettersTopRow = ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p'];
const lettersMiddleRow = ['a','s', 'd', 'f', 'g', 'h', 'j', 'k', 'l'];
const lettersBottomRow = ['z', 'x', 'c', 'v', 'b', 'n', 'm']

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

function isGuessedLetter(letter) {
  return correctlyGuessedLetters.value.includes(letter) || incorrectlyGuessedLetters.value.includes(letter);
}

function isCurrentGuess(letter) {
  return letter.toLowerCase() === guess.value.toLowerCase();
}

function setGuess(letter) {
  guess.value = letter;
}

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
    window.scrollTo({
      top: 0,
      behavior: "smooth",
    });
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
  if (!props.isMobile) {
    document.getElementById('input').focus();
  }
});
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
    margin-top: 30px;
  }

  .game {
    text-align: center;
    margin-top: 25px;
  }

  .dashes {
    font-size: 36px;
  }

  .letterRow {
    margin: 10px 0 10px 0;
  }

  .letters {
    display: inline-block;
    min-width: 30px;
    font-size: 30px;
    border: 1px solid rgb(206, 206, 206);
    margin: 0 1px;
  }

  .guessed {
    background: gray;
  }

  .currentGuess {
    background: yellow;
  }

  #guessValue {
    display: inline-block;
    min-width: 35px;
    min-height: 34px;
    font-size: 25px;
    border-bottom: 1px solid black;
    margin: 0px 15px -10px 15px;
  }

  .input-form {
    font-size: 24px;
  }

  #input {
    font-size: 18px;
    text-align: center;
  }

  .guessSubmit {
    min-height: 50px;
  }

  #submit-button {
    font-size: 18px;
  }

  #submit-button-mobile {
    display: inline-block;
    font-size: 18px;
    margin-bottom: 20px
  }

  .s0 {
    fill: none;
    stroke: #000000;
    stroke-width: 8;
    animation: draw 2s forwards;
    stroke-dasharray: 572;
    stroke-dashoffset: 571;
  }

  #Face {
    fill: #ffffff;
    stroke: #000000;
    stroke-miterlimit: 100;
    stroke-width: 4;
  }

  .red {
    color: red;
  }

  .green {
    color: green;
  }

  .fade-in {
    opacity: 1;
    animation: fadeInOpacity 0.5s  ease-in;
  }

  @keyframes draw {
    to {
      stroke-dashoffset: 0;
    }
  }

  @keyframes fadeInOpacity {
    0% {
      opacity: 0;
    }
    100% {
      opacity: 1;
    }
  }
</style>
