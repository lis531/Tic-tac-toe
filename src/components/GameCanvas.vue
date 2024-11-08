<script setup>
import { ref } from 'vue';

const buttonStates = ref(Array(9).fill(''));
let lastPlayer = 'X';
let lastStartingPlayer = 'X';
let isAllowed = true;

const selectedGameMode = ref(1);
const lastButtonHovered = ref(false);

const toggleGameMode = (mode) => {
  selectedGameMode.value = mode;
  lastPlayer = 'X';
  reset();
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
  if (!isAllowed) return;
  if (buttonStates.value[index] !== '') return;

  const p = document.querySelectorAll('.buttonGrid p');
  p[index].style.transform = 'scale(0.75)';
  setTimeout(() => {
    p[index].style.transform = 'scale(1)';
  }, 200);

  buttonStates.value[index] = selectedGameMode.value === 1 ? 'X' : lastPlayer;

  if (checkWinner('X')) {
    checkGameStatus();
    return;
  }
    console.log("this shit");

  if (selectedGameMode.value === 1) {
    const emptyIndexes = getIndexes('');
    isAllowed = false;
    setTimeout(() => {
      let chosenIndex = getWinningMove('O') || getWinningMove('X') || emptyIndexes[Math.floor(Math.random() * emptyIndexes.length)];
      p[chosenIndex].style.transform = 'scale(0.75)';
      setTimeout(() => {
        p[chosenIndex].style.transform = 'scale(1)';
      }, 200);
      isAllowed = true;
      buttonStates.value[chosenIndex] = 'O';
      checkGameStatus();
    }, 200);
  } else {
    lastPlayer = lastPlayer === 'X' ? 'O' : 'X';
    checkGameStatus();
  }
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
  const getRestartButton = document.querySelector('.reset-button');
  if (checkWinner('X') || checkWinner('O') || !buttonStates.value.includes('')) {
    getRestartButton.style.display = 'block';
    isAllowed = false;
  }
  if (checkWinner('X')) {
    message.textContent = selectedGameMode.value === 1 ? 'You won!' : 'Player X won!';
  } else if (checkWinner('O')) {
    message.textContent = selectedGameMode.value === 1 ? 'You lost!' : 'Player O won!';
  } else if (!buttonStates.value.includes('')) {
    message.textContent = "It's a draw!";
  }
};

const placeRandomO = () => {
  lastStartingPlayer = 'O';
  const emptyIndexes = getIndexes('');
  let chosenIndex = emptyIndexes[Math.floor(Math.random() * emptyIndexes.length)];
  const p = document.querySelectorAll('.buttonGrid p');
  buttonStates.value[chosenIndex] = 'O';
  p[chosenIndex].style.transform = 'scale(0.75)';
  setTimeout(() => {
    p[chosenIndex].style.transform = 'scale(1)';
  }, 200);
};

const reset = () => {
  isAllowed = true;
  buttonStates.value = Array(9).fill('');
  const message = document.getElementById('message');
  message.textContent = '';
  const getRestartButton = document.querySelector('.reset-button');
  getRestartButton.style.display = 'none';

  if (selectedGameMode.value === 1 && lastStartingPlayer === 'X') {
    placeRandomO();
  } else {
    lastStartingPlayer = 'X';
  }
};
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
        class="buttonGrid"
        v-for="(state, index) in buttonStates"
        :key="index"
        @click="toggleButtonState(index)">
      <p>{{ state }}</p>
    </button>
    <button class="reset-button" @click="reset()">
      Reset
    </button>
  </grid>
</template>

<style scoped>
grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.5rem;
}
.buttonGrid {
  font-size: 5rem;
  padding: 1rem;
  width: 10rem;
  height: 10rem;
  border: 0;
  border-radius: 0.5rem;
  background-color: rgb(var(--color-background-soft));
  color: rgb(var(--color-text));
  cursor: pointer;
  transition: ease-in-out 0.2s;
  user-select: none;
  box-shadow: 0px 0px 5px 0px rgba(0, 0, 0, 0.2);
}
.buttonGrid > p {
  margin: 0;
  transition: ease-in-out 0.2s;
}
.buttons {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1vh;
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
  color: rgb(var(--color-text));
  background: none;
}
.buttons::after {
  content: '';
  position: absolute;
  background-color: rgb(var(--color-background-soft));
  backdrop-filter: blur(0.5rem);
  height: calc(100% - 2rem);
  width: 12rem;
  border-radius: 1rem;
  z-index: -1;
  transition: transform 0.4s ease-in-out;
  left: 1rem;
  transform: translateX(-100%);
}
.reset-button {
  display: none;
  position: absolute;
  align-self: center;
  justify-self: center;
  backdrop-filter: blur(3px);
  border-radius: 1rem;
  font-size: 2rem;
  padding: 1rem 2rem;
  background-color: rgba(var(--color-background-almost), 0.9);
  border: 1px solid rgb(var(--color-background));
  cursor: pointer;
  color: rgb(var(--color-text));
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