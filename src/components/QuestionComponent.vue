<template>
  <transition name="fade">
    <div class="question-popup" v-if="question">
      <h2>{{ question }}</h2>
      <div class="answers">
        <div v-for="(answer, index) in answers" :key="index" class="answer">
          <img :src="answer.image" @click="checkAnswer(answer)" />
        </div>
      </div>
    </div>
  </transition>
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
.question-popup {
  width: 70%;
  margin: auto;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.answers {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}

.answer {
  cursor: pointer;
  margin: 10px;
}

.answer img {
  width: 150px;
  height: 150px;
  object-fit: cover;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}
</style>
