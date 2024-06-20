<template>
  <div class="info">
    <p>Score: {{ score }}</p>
    <p>Lives: {{ lives }}</p>
    <p>Time: {{ elapsedTime }} seconds</p>
  </div>
</template>

<script>
export default {
  name: 'GameInformation',
  props: {
    score: Number,
    lives: Number,
    elapsedTime: Number
  }
}
</script>

<style scoped>
.info {
  margin-top: 10vh;
}
</style>
