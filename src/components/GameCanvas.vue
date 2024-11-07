<script setup>
import { ref } from 'vue';

const buttonStates = ref(Array(9).fill(''));
let lastPlayer = 'X';

const selectedGameMode = ref(1);
const lastButtonHovered = ref(false);

const toggleGameMode = (mode) => {
  selectedGameMode.value = mode;
  buttonStates.value = Array(9).fill('');
  lastPlayer = 'X';
  let message = document.getElementById('message');
  message.textContent = '';
};

const handleMouseEnter = (buttonType) => {
  if (buttonType === 'last') lastButtonHovered.value = true;
};

const handleMouseLeave = () => {
  lastButtonHovered.value = false;
};

const checkWinner = (type) => {
  const winningCombos = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  return winningCombos.some((combo) => {
    return combo.every((index) => buttonStates.value[index] === type);
  });
};

const toggleButtonState = (index) => {
  let message = document.getElementById('message');
  if(selectedGameMode === 1) {
    if(buttonStates.value[index] === '') {
      buttonStates.value[index] = 'X';
      const emptyIndexes = buttonStates.value.reduce((acc, state, index) => {
        if(state === '') {
          acc.push(index);
        }
        return acc;
      }, []);
      const randomIndex = emptyIndexes[Math.floor(Math.random() * emptyIndexes.length)];
      buttonStates.value[randomIndex] = 'O';
    }
    if(checkWinner('X')) {
      message.textContent = 'You won!';
      buttonStates.value = Array(9).fill('');
    } else if(checkWinner('O')) {
      message.textContent = 'You lost!';
      buttonStates.value = Array(9).fill('');
    } else if(!buttonStates.value.includes('')) {
      message.textContent = 'It\'s a draw!';
      buttonStates.value = Array(9).fill('');
    }
  } else {
    if(buttonStates.value[index] === '') {
      buttonStates.value[index] = lastPlayer;
      lastPlayer = lastPlayer === 'X' ? 'O' : 'X';
    }
    if(checkWinner('X')) {
      message.textContent = 'Player X won!';
      buttonStates.value = Array(9).fill('');
    } else if(checkWinner('O')) {
      message.textContent = 'Player O won!';
      buttonStates.value = Array(9).fill('');
    } else if(!buttonStates.value.includes('')) {
      message.textContent = 'It\'s a draw!';
      buttonStates.value = Array(9).fill('');
    }
  }
};
</script>

<template>
  <div class="buttons" :class="{ 'mode-1-selected': selectedGameMode === 1, 'mode-2-selected': selectedGameMode === 2 }">
    <button
        @click="toggleGameMode(1)"
        @mouseenter="handleMouseEnter('first')"
        @mouseleave="handleMouseLeave">Single Player</button>
    <button
        @click="toggleGameMode(2)"
        @mouseenter="handleMouseEnter('last')"
        @mouseleave="handleMouseLeave">Two Players</button>
  </div>
  <p id="message"></p>
  <grid :rows="3" :columns="3">
    <button
        v-for="(state, index) in buttonStates"
        :key="index"
        @click="toggleButtonState(index)">
      {{ state }}
    </button>
  </grid>
</template>

<style scoped>
grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
}
grid > button {
  font-size: 5rem;
  padding: 1rem;
  width: 10rem;
  height: 10rem;
  border: 0;
  border-radius: 0.5rem;
  background-color: var(--color-background-soft);
  color: var(--color-text);
  cursor: pointer;
  transition: ease-in-out 0.2s;
  user-select: none;
  box-shadow: 0px 0px 5px 0px rgba(0, 0, 0, 0.2);
}
.buttons {
  display: flex;
  justify-content: space-between;
  margin-bottom: 2vh;
  background-color: rgba(var(--color-background-almost), 0.5);
  padding: 1rem;
  border-radius: 1rem;
  position: relative;
}
.buttons > button {
  font-size: 1.5rem;
  padding: 0.5rem 1rem;
  border: 0;
  width: 12rem;
  border-radius: 0.5rem;
  cursor: pointer;
  user-select: none;
  color: var(--color-text);
  background: none;
}
.buttons::after {
  content: '';
  position: absolute;
  background-color: var(--color-background-soft);
  backdrop-filter: blur(0.5rem);
  height: calc(100% - 2rem);
  width: 12rem;
  border-radius: 1rem;
  z-index: -1;
  transition: transform 0.4s ease-in-out;
  left: 1rem;
  transform: translateX(-100%);
}
.mode-1-selected::after {
  transform: translateX(0);
}
.mode-2-selected::after {
  transform: translateX(100%);
}

#message {
  font-size: 1.5rem;
  margin-bottom: 2vh;
}
</style>