<template>
  <Loading v-if="fetching"/>
  <Error v-if="errorState" :error-message="errorMessage"/>
  <Hangman v-else :word="word"/>
</template>

<script setup>
import { ref, onBeforeMount } from 'vue';
import Hangman from './components/Hangman.vue';
import Loading from './components/Loading.vue';
import Error from './components/Error.vue';

const fetching = ref(false);
const errorState = ref(false);
const errorMessage = ref();
const word = ref();

async function fetchRandomWord() {
  const randomWordUrl = "https://random-word-api.herokuapp.com/word";
  fetching.value = true;

  try {
    const response = await fetch(randomWordUrl);
    const generatedWord = await response.json();

    fetching.value = false;
    word.value = generatedWord[0];
    console.log(generatedWord[0]);
  } catch (error) {

    fetching.value = false;
    errorState.value = true;
    errorMessage.value = error.message;
    console.error(error);
  }
}

onBeforeMount(() => {
  fetchRandomWord();
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
  justify-content: center;
}
</style>
