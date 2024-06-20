<template>
  <div class="question-popup">
    <h1 class="question-text" v-if="question">{{ question }}</h1>
    <div class="answers" v-if="question">
      <div v-for="(answer, index) in answers" :key="index" class="answer" @click="checkAnswer(answer)">
        <img :src="answer.image" />
        <div class="overlay" :class="{ correct: answer.correct, wrong: answer.wrong }" v-if="answer.checked"></div>
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
  position: relative;
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

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.5);
}

.overlay.correct {
  background-color: rgba(0, 255, 0, 0.5);
}

.overlay.wrong {
  background-color: rgba(255, 0, 0, 0.5);
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

.question-text {
  margin-bottom: 10vh;
  margin-top: 5vh;
  font-weight: bold;
  font-size: large;
  font-size: 2rem; 
  opacity: 0; 
  animation: slide-in 1s ease-out forwards; 
}

@keyframes slide-in {
  from {
    transform: translateX(50px); 
    opacity: 0; 
  }
  to {
    transform: translateX(0); 
    opacity: 1; 
  }
}
</style>
