<template>
  <div id="app">
    <div v-if="gameOver">
      <ResultComponent :score="score" :totalTime="totalTime" @restart-game="restartGame"/>
    </div>
    <div v-else>
      <QuestionComponent
        :question="currentQuestion.question"
        :answers="answers"
        @answer-selected="handleAnswer"
      />
      <div class="info">
        <p>Score: {{ score }}</p>
        <p>Lives: {{ lives }}</p>
        <p>Time: {{ elapsedTime }} seconds</p>
      </div>
    </div>
  </div>
</template>

<script>
import QuestionComponent from './components/QuestionComponent.vue'
import ResultComponent from './components/ResultComponent.vue'

export default {
  name: 'App',
  components: {
    QuestionComponent,
    ResultComponent
  },
  data() {
    return {
      questions: [
        { question: 'Which one is a cat?', answer: require('./assets/cat.jpg') },
        { question: 'Which one is a dog?', answer: require('./assets/dog.jpg') },
        { question: 'Which one is a platypus?', answer: require('./assets/platypus.jpg') },
        { question: 'Which one is a capybara?', answer: require('./assets/capy.jpg') },
        { question: 'Which one is a rabbit?', answer: require('./assets/rabbit.jpg') }
      ],
      answers: [
        { image: require('./assets/cat.jpg') },
        { image: require('./assets/dog.jpg') },
        { image: require('./assets/platypus.jpg') },
        { image: require('./assets/capy.jpg') },
        { image: require('./assets/rabbit.jpg') }
      ],
      currentQuestionIndex: 0,
      score: 0,
      lives: 3,
      gameOver: false,
      startTime: null,
      elapsedTime: 0,
      timer: null
    }
  },
  computed: {
    currentQuestion() {
      return this.questions[this.currentQuestionIndex];
    },
    totalTime() {
      return this.elapsedTime;
    }
  },
  methods: {
    startTimer() {
      this.startTime = Date.now();
      this.timer = setInterval(() => {
        this.elapsedTime = Math.floor((Date.now() - this.startTime) / 1000);
      }, 1000);
    },
    stopTimer() {
      clearInterval(this.timer);
    },
    handleAnswer(selectedAnswer) {
      if (selectedAnswer.image === this.currentQuestion.answer) {
        this.score++;
        this.answers = this.answers.filter(answer => answer.image !== selectedAnswer.image);
      } else {
        this.lives--;
      }
      
      this.currentQuestionIndex++;

      if (this.lives <= 0 || this.currentQuestionIndex >= this.questions.length) {
        this.stopTimer();
        this.gameOver = true;
      }
    },
    restartGame() {
      this.currentQuestionIndex = 0;
      this.score = 0;
      this.lives = 3;
      this.gameOver = false;
      this.elapsedTime = 0;
      this.answers = [
        { image: require('./assets/cat.jpg') },
        { image: require('./assets/dog.jpg') },
        { image: require('./assets/platypus.jpg') },
        { image: require('./assets/capy.jpg') },
        { image: require('./assets/rabbit.jpg') }
      ];
      this.startTimer();
    }
  },
  mounted() {
    this.startTimer();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 60px;
}

.info {
  margin-top: 20px;
}
</style>
