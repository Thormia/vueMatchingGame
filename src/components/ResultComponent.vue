<template>
    <div>
      <h2>Game Over</h2>
      <p>Your score: {{ score }}</p>
      <p>Total time: {{ totalTime }} seconds</p>
      <button @click="restartGame">Restart</button>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      score: Number,
      totalTime: Number
    },
    methods: {
      restartGame() {
        this.$emit('restart-game');
      }
    }
  }
  </script>
  
  <style scoped>
  button {
    margin: 5px;
  }
  </style>
  