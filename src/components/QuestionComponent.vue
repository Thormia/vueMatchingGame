<template>
    <div class="question-popup">
      <h2>{{ question }}</h2>
      <div class="answers">
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
  .question-popup {
    width: 70%;
    margin: auto;
    padding: 20px;
    background: #f9f9f9;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    animation: fadeIn 0.5s ease-in-out;
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
  
  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: scale(0.9);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }
  </style>
  