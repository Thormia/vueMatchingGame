<template>
  <div id="app">
    <button class="menu-btn" @click="toggleMenu">Menu</button>
    <button class="mute-btn" @click="toggleMute">{{ isMuted ? 'Unmute' : 'Mute' }}</button>

    <div v-if="gameOver">
      <ResultComponent :score="score" :totalTime="totalTime" @restart-game="restartGame"/>
    </div>
    <div v-else>
      <transition name="fade">
        <QuestionComponent
          :key="currentQuestionIndex"
          :question="currentQuestion.question"
          :answers="answers"
          @answer-selected="handleAnswer"
        />
      </transition>
      <div class="info">
        <p>Score: {{ score }}</p>
        <p>Lives: {{ lives }}</p>
        <p>Time: {{ elapsedTime }} seconds</p>
      </div>
    </div>

    <div v-if="showMenu" class="menu-popup">
      <button @click="restartGame">Start Again</button>
      <button @click="continueGame">Continue Playing</button>
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
      timer: null,
      showMenu: false,
      isMuted: false,
      backgroundAudio: null
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
      this.startTime = Date.now() - this.elapsedTime * 1000;
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
      this.showMenu = false;
    },
    toggleMenu() {
      this.showMenu = !this.showMenu;
      if (this.showMenu) {
        this.stopTimer();
      } else {
        this.startTimer();
      }
    },
    continueGame() {
      this.showMenu = false;
      this.startTimer();
    },
    toggleMute() {
      this.isMuted = !this.isMuted;
      if (this.isMuted) {
        this.backgroundAudio.pause();
      } else {
        this.backgroundAudio.play();
      }
    }
  },
  mounted() {
    this.startTimer();
    this.backgroundAudio = new Audio(require('./assets/background.mp3'));
    this.backgroundAudio.loop = true;
    if (!this.isMuted) {
      this.backgroundAudio.play();
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  background: url('./assets/background.jpg') no-repeat center center fixed;
  background-size: cover;
  height: 100vh;
  overflow: hidden;
}

.info {
  margin-top: 20px;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}

.menu-btn, .mute-btn {
  position: absolute;
  top: 10px;
  padding: 10px;
  border: none;
  background: #333;
  color: #fff;
  cursor: pointer;
}

.menu-btn {
  left: 10px;
}

.mute-btn {
  right: 10px;
}

.menu-popup {
  position: fixed;
  top: 50%;
  left: 50%;
  width: 20vw;
  height: 20vh;
  transform: translate(-50%, -50%);
  background: rgba(255, 255, 255, 0.9);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
}

.menu-popup button {
  margin: 10px;
  padding: 10px;
  border: none;
  background: #333;
  color: #fff;
  cursor: pointer;
}
</style>
