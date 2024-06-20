<template>
  <div id="app">
    <GameMenu 
      :isMuted="isMuted"
      :showMenu="showMenu"
      @toggle-menu="toggleMenu"
      @toggle-mute="toggleMute"
      @restart-game="restartGame"
      @continue-game="continueGame"
    />
    
    <div v-if="gameOver">
      <ResultComponent :score="score" :totalTime="totalTime" @restart-game="restartGame" @show-answers="showAnswers"/>
    </div>
    <div v-else-if="showAnswersMode">
      <div v-for="(question, index) in questions" :key="index" class="answer-display">
        <p>{{ question.question }}</p>
        <img :src="question.answer" />
      </div>
      <button class="restart" @click="restartGame">Restart</button>
    </div>
    <div v-else>
      <GameInformation :score="score" :lives="lives" :elapsedTime="elapsedTime" />
      <QuestionComponent
        :key="currentQuestionIndex"
        :question="currentQuestion.question"
        :answers="currentAnswers"
        @answer-selected="handleAnswer"
      />
    </div>
  </div>
</template>

<script>
import QuestionComponent from './components/QuestionComponent.vue';
import ResultComponent from './components/ResultComponent.vue';
import GameMenu from './components/GameMenu.vue';
import GameInformation from './components/GameInformation.vue';
import correctSound from './assets/correct.mp3';
import wrongSound from './assets/wrong.mp3';
import backgroundSound from './assets/background.mp3';

export default {
  name: 'App',
  components: {
    QuestionComponent,
    ResultComponent,
    GameMenu,
    GameInformation
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
      showAnswersMode: false,
      startTime: null,
      elapsedTime: 0,
      timer: null,
      showMenu: false,
      isMuted: true,
      backgroundAudio: null,
      correctAudio: null,
      wrongAudio: null,
      userInteracted: false
    };
  },
  computed: {
    currentQuestion() {
      return this.questions[this.currentQuestionIndex];
    },
    currentAnswers() {
      return this.answers;
    },
    totalTime() {
      return this.elapsedTime;
    }
  },
  methods: {
    shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    startTimer() {
      this.startTime = Date.now() - this.elapsedTime * 1000;
      this.timer = setInterval(() => {
        this.elapsedTime = Math.floor((Date.now() - this.startTime) / 1000);
      }, 1000);
    },
    stopTimer() {
      clearInterval(this.timer);
    },
    handleAnswer(selectedAnswer) {
      selectedAnswer.checked = true;
      if (selectedAnswer.image === this.currentQuestion.answer) {
        this.score++;
        selectedAnswer.correct = true;
        if (!this.isMuted) {
          this.correctAudio.play();
        }
        setTimeout(() => {
          this.answers = this.answers.filter(answer => answer.image !== selectedAnswer.image);
          this.moveToNextQuestion();
        }, 1000);
      } else {
        this.lives--;
        selectedAnswer.wrong = true;
        if (!this.isMuted) {
          this.wrongAudio.play();
        }
        setTimeout(() => {
          this.moveToNextQuestion();
        }, 1000);
      }
    },
    moveToNextQuestion() {
      this.currentQuestionIndex++;
      this.answers.forEach(answer => {
        answer.checked = false;
        answer.correct = false;
        answer.wrong = false;
      });
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
      this.showAnswersMode = false;
      this.elapsedTime = 0;
      this.questions = this.shuffle(this.questions);
      this.answers = [
        { image: require('./assets/cat.jpg') },
        { image: require('./assets/dog.jpg') },
        { image: require('./assets/platypus.jpg') },
        { image: require('./assets/capy.jpg') },
        { image: require('./assets/rabbit.jpg') }
      ];
      this.answers = this.shuffle(this.answers);
      this.startTimer();
      this.showMenu = false;
      if (!this.isMuted && this.userInteracted) {
        this.backgroundAudio.play();
      }
    },
    toggleMenu() {
      this.showMenu = !this.showMenu;
      if (this.showMenu) {
        this.stopTimer();
        this.backgroundAudio.pause();
      } else {
        this.startTimer();
        if (!this.isMuted && this.userInteracted) {
          this.backgroundAudio.play();
        }
      }
    },
    continueGame() {
      this.showMenu = false;
      this.startTimer();
      if (!this.isMuted && this.userInteracted) {
        this.backgroundAudio.play();
      }
    },
    toggleMute() {
      this.isMuted = !this.isMuted;
      if (this.isMuted) {
        this.backgroundAudio.pause();
      } else {
        this.backgroundAudio.play();
      }
    },
    showAnswers() {
      this.gameOver = false;
      this.showAnswersMode = true;
    },
    handleInteraction() {
      this.userInteracted = true;
      this.toggleMenu();
      if (!this.isMuted) {
        this.backgroundAudio.play();
      }
    }
  },
  mounted() {
    this.startTimer();
    this.questions = this.shuffle(this.questions);
    this.answers = this.shuffle(this.answers);
    
    this.backgroundAudio = new Audio(backgroundSound);
    this.backgroundAudio.loop = true;

    this.correctAudio = new Audio(correctSound);
    this.wrongAudio = new Audio(wrongSound);

    this.backgroundAudio.addEventListener('error', (e) => {
      console.error('Error loading background audio:', e);
    });

    this.backgroundAudio.load();
  }
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
  font-family: "Lucida Console", Courier, monospace;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  background: url('./assets/background.png') no-repeat center center fixed;
  background-size: cover;
  height: 100vh;
  overflow: hidden;
}

.restart{
  margin-left: 35vw;
  margin-top: 10vh;
  display: block;
  width: 30vw;
  padding: 10px;
  border: none;
  background: #333;
  color: #fff;
  cursor: pointer;
}

.answer-display {
  margin: 1.042vw;
  display: flex;
  display: inline-block;
}

.answer-display img {
  width: 150px;
  height: 150px;
  object-fit: cover;
}
</style>
