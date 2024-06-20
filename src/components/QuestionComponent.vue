<template>
  <transition name="fade">
    <div class="question-popup" v-if="question">
      <h1>{{ question }}</h1>
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

h1{
  margin-top: 5vh;
}

.question-popup {
  margin-top: 10vh;
  font-size: 1.563vw;
  margin-left: 15vw;
  height: 55vh;
  width: 70%;
  padding: 	1.042vw;
  background: #f9f9f9;
  border-radius: 	1.042vw;
  box-shadow: 0 0 1.042vw rgba(0, 0, 0, 0.1);
}

.answers {
  margin-top: 10vh;
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}

.answer {
  cursor: pointer;
  margin: 10px;
}

.answer img {
  width: 19vh;
  height: 19vh;
  object-fit: cover;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}
</style>
