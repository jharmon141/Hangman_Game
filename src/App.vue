<template>
  <Loading v-if="fetching"/>
  <Error v-else-if="errorState" :error-message="errorMessage"/>
  <Hangman v-else :word="word" :restartGame="fetchRandomWord" :isMobile="isMobile"/>
</template>

<script setup>
import { ref, onBeforeMount } from 'vue';
import Hangman from './components/Hangman.vue';
import Loading from './components/Loading.vue';
import Error from './components/Error.vue';

const fetching = ref(false);
const errorState = ref(false);
const errorMessage = ref();
const isMobile = ref(false);
const word = ref();

async function fetchRandomWord() {
  const randomWordUrl = "https://random-word-api.herokuapp.com/word";
  fetching.value = true;

  try {
    const response = await fetch(randomWordUrl);
    const generatedWord = await response.json();

    fetching.value = false;
    word.value = generatedWord[0];
  } catch (error) {
    fetching.value = false;
    errorState.value = true;
    errorMessage.value = error.message;
    console.error(error);
  }
}

function setDeviceType() {
  const devicesRegex = /Mobi|Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini|Silk|HTC_/i;
  isMobile.value = devicesRegex.test(navigator.userAgent);
}

onBeforeMount(() => {
  fetchRandomWord();
  setDeviceType();
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  height: 100vh;
  display: flex;
  flex-direction: column;
}
</style>
