<template>
  <div>
    <button class="menu-btn" @click="toggleMenu">Menu</button>
    <button class="mute-btn" @click="toggleMute">{{ isMuted ? 'Unmute' : 'Mute' }}</button>

    <div v-if="showMenu" class="menu-popup">
      <button @click="restartGame">Start Again</button>
      <button @click="continueGame">Continue Playing</button>
    </div>
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
