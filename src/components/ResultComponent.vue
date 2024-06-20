<template>
  <div class="game-over" >
    <h2 >Game Over</h2>
    <p>Your score: {{ score }}</p>
    <p>Total time: {{ totalTime }} seconds</p>
    <button @click="restartGame">Restart</button>
    <button @click="showAnswers">Show Answers</button>
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
    },
    showAnswers() {
      this.$emit('show-answers');
    }
  }
}
</script>

<style scoped>

h2{
  font-size: 2.604vw;
  color: red;
  margin-bottom: 3vh;
}

.game-over button {
  margin-top: 3.5vh;
  padding: 1.5vh;
  border: none;
  background: #333;
  color: #fff;
  cursor: pointer;
}

.game-over {
  margin-top: 25vh;
  font-size: 1.563vw;
  margin-left: 12.5vw;
  height: 31.9vh;
  width: 70%;
  padding: 2.084vw;
  padding-top: 9vh;
  background: #f9f9f9;
  border-radius: 1.042vw;
  box-shadow: 0 0 1.042vw rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
}

button {
  margin: 5px;
}
</style>
