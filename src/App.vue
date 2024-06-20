<template>
  <div id="app">
    <button class="menu-btn" @click="handleInteraction">Menu</button>
    <button class="mute-btn" @click="toggleMute">{{ isMuted ? 'Unmute' : 'Mute' }}</button>

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
      <div class="info">
        <p>Score: {{ score }}</p>
        <p>Lives: {{ lives }}</p>
        <p>Time: {{ elapsedTime }} seconds</p>
      </div>
      <transition name="fade">
        <QuestionComponent
          :key="currentQuestionIndex"
          :question="currentQuestion.question"
          :answers="answers"
          @answer-selected="handleAnswer"
        />
      </transition>
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
import correctSound from './assets/correct.mp3'
import wrongSound from './assets/wrong.mp3'
import backgroundSound from './assets/background.mp3'

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
        if (!this.isMuted) {
          this.correctAudio.play();
        }
      } else {
        this.lives--;
        if (!this.isMuted) {
          this.wrongAudio.play();
        }
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
      this.showAnswersMode = false;
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

.info {
  margin-top: 10vh;
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
