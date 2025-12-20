<template>
    <div id="loading-spinner">
        <img alt="Hangman Logo" id="top-logo" src="../assets/HangmanLogo.svg">
    </div>
</template>

<style scoped>
#loading-spinner {
  margin-top: 250px;
  display: block;
  justify-content: center;
}

#top-logo {
  width: 250px;
  height: auto;
  display: block;
  -webkit-animation:spin 3s linear infinite;
  -moz-animation:spin 3s linear infinite;
  animation:spin 3s linear infinite;
}

@-moz-keyframes spin { 
    100% { -moz-transform: rotate(360deg); } 
}
@-webkit-keyframes spin { 
    100% { -webkit-transform: rotate(360deg); } 
}
@keyframes spin { 
    100% { 
        -webkit-transform: rotate(360deg); 
        transform:rotate(360deg); 
    } 
}
</style>