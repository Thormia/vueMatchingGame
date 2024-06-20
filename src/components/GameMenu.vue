<template>
  <div>
    <!-- Menu Button -->
    <button class="menu-btn" @click="toggleMenu">Menu</button>
    
    <!-- Mute Button -->
    <button class="mute-btn" @click="toggleMute">{{ isMuted ? 'Unmute' : 'Mute' }}</button>

    <!-- Menu Popup -->
    <transition name="menu-fade">
      <div v-if="showMenu" class="menu-overlay">
        <div class="menu-popup">
          <button @click="restartGame">Start Again</button>
          <button @click="continueGame">Continue Playing</button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'GameMenu',
  props: {
    isMuted: Boolean,
    showMenu: Boolean
  },
  methods: {
    toggleMenu() {
      this.$emit('toggle-menu');
    },
    toggleMute() {
      this.$emit('toggle-mute');
    },
    restartGame() {
      this.$emit('restart-game');
    },
    continueGame() {
      this.$emit('continue-game');
    }
  }
}
</script>

<style scoped>
.menu-btn, .mute-btn {
  position: absolute;
  top: 10px;
  padding: 10px;
  border: none;
  background: #333;
  color: #fff;
  cursor: pointer;
  z-index: 100; /* Ensure buttons are above other content */
}

.menu-btn {
  left: 10px;
}

.mute-btn {
  right: 10px;
}

.menu-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5); 
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 99; 
}

.menu-popup {
  width: 20vw;
  height: 20vh;
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

.menu-fade-enter-active, .menu-fade-leave-active {
  transition: opacity 0.5s;
}

.menu-fade-enter, .menu-fade-leave-to {
  opacity: 0;
}
</style>
