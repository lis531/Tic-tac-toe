<script setup>
import { ref, onMounted } from 'vue';

const buttonStates = ref(Array(9).fill(''));
let lastPlayer = 'X';
let lastStartingPlayer = 'X';

const selectedGameMode = ref(1);
const lastButtonHovered = ref(false);

const toggleGameMode = (mode) => {
  selectedGameMode.value = mode;
  buttonStates.value = Array(9).fill('');
  lastPlayer = 'X';
  let message = document.getElementById('message');
  message.textContent = '';

  if (mode === 1) {
    placeRandomO();
  }
};

const handleMouseEnter = (buttonType) => {
  if (buttonType === 'last') lastButtonHovered.value = true;
};

const handleMouseLeave = () => {
  lastButtonHovered.value = false;
};

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

const checkWinner = (type) => {
  return winningCombos.some(combo => combo.every(index => buttonStates.value[index] === type));
};

const toggleButtonState = (index) => {
  if (buttonStates.value[index] !== '') return;

  buttonStates.value[index] = selectedGameMode.value === 1 ? 'X' : lastPlayer;

  if (selectedGameMode.value === 1) {
    const emptyIndexes = getIndexes('');
    let chosenIndex = getWinningMove('O') || getWinningMove('X') || emptyIndexes[Math.floor(Math.random() * emptyIndexes.length)];
    buttonStates.value[chosenIndex] = 'O';
  } else {
    lastPlayer = lastPlayer === 'X' ? 'O' : 'X';
  }

  checkGameStatus();
};

const getIndexes = (mark) => {
  const indexes = [];
  for (let i = 0; i < buttonStates.value.length; i++) {
    if (buttonStates.value[i] === mark) {
      indexes.push(i);
    }
  }
  return indexes;
};

const getWinningMove = (player) => {
  const playerIndexes = getIndexes(player);
  const emptyIndexes = getIndexes('');
  for (let j = 0; j < winningCombos.length; j++) {
    const playerInCombo = winningCombos[j].filter(idx => playerIndexes.includes(idx));
    const emptyInCombo = winningCombos[j].filter(idx => emptyIndexes.includes(idx));
    if (playerInCombo.length === 2 && emptyInCombo.length === 1) {
      return emptyInCombo[0];
    }
  }
  return null;
};

const checkGameStatus = () => {
  const message = document.getElementById('message');
  if (checkWinner('X')) {
    message.textContent = selectedGameMode.value === 1 ? 'You won!' : 'Player X won!';
  } else if (checkWinner('O')) {
    message.textContent = selectedGameMode.value === 1 ? 'You lost!' : 'Player O won!';
  } else if (!buttonStates.value.includes('')) {
    message.textContent = "It's a draw!";
  } else {
    return;
  }
  buttonStates.value = Array(9).fill('');
  if(lastStartingPlayer !== 'O') {
    placeRandomO();
  } else {
    lastStartingPlayer = 'X';
  }
};

const placeRandomO = () => {
  lastStartingPlayer = 'O';
  const emptyIndexes = getIndexes('');
  let chosenIndex = emptyIndexes[Math.floor(Math.random() * emptyIndexes.length)];
  buttonStates.value[chosenIndex] = 'O';
};

onMounted(() => {
  if (selectedGameMode.value === 1) {
    placeRandomO();
  }
});
</script>

<template>
  <p id="message"></p>
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
  margin-bottom: 1vh;
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