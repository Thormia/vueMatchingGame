<template>
  <div class="question-popup">
    <div class="question-container">
      <h1 v-if="question">{{ question }}</h1>
    </div>
    <div class="answers" v-if="question">
      <div v-for="(answer, index) in answers" :key="index" class="answer">
        <img :src="answer.image" @click="checkAnswer(answer)" />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    question: String,
    answers: Array
  },
  methods: {
    checkAnswer(answer) {
      this.$emit('answer-selected', answer);
    }
  }
}
</script>

<style scoped>
.answer {
  margin: 1.042vw;
  display: inline-block;
  transition: transform 0.3s, box-shadow 0.3s;
  cursor: pointer;
}

.answer img {
  width: 19vh;
  height: 19vh;
  object-fit: cover;
}

.answer:hover {
  transform: scale(1.1);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.question-popup {
  margin-top: 10vh;
  font-size: 1.563vw;
  margin-left: 15vw;
  height: 55vh;
  width: 70%;
  padding: 1.042vw;
  background: #f9f9f9;
  border-radius: 1.042vw;
  box-shadow: 0 0 1.042vw rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
}

.question-container {
  margin-top: 5vh;
  width: 100%;
  text-align: center;
}

.answers {
  margin-top: 10vh;
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  width: 100%;
}
</style>
