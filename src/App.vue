<script setup>
import { ref, watch } from "vue";
import { storeToRefs } from "pinia";
import { useGameStore } from "./stores/GameStore";
import { launchConfetti } from "./utilities/confetti";
import GameBoard from "./components/GameBoard.vue";
import NewGameButton from "./components/NewGameButton.vue";

const userSelection = ref([]);
const userCanFlipCard = ref(true);

const gameStore = useGameStore();
const { cardList, matchesFound, status } = storeToRefs(gameStore);

const flipCard = payload => {
  if (userCanFlipCard.value) {
    cardList.value[payload.position].visible = true;

    if (userSelection.value[0]) {
      if (
        userSelection.value[0].position === payload.position &&
        userSelection.value[0].faceValue === payload.faceValue
      ) {
        return;
      } else {
        userSelection.value[1] = payload;
      }
    } else {
      userSelection.value[0] = payload;
    }
  } else {
    return;
  }
};

watch(matchesFound, currentValue => {
  if (currentValue === 9) {
    launchConfetti();
  }
});

watch(
  userSelection,
  currentValue => {
    if (currentValue.length === 2) {
      const cardOne = currentValue[0];
      const cardTwo = currentValue[1];
      // Deshabilitar la capacidad de voltear cartas
      userCanFlipCard.value = false;

      if (cardOne.faceValue === cardTwo.faceValue) {
        cardList.value[cardOne.position].matched = true;
        cardList.value[cardTwo.position].matched = true;
        userCanFlipCard.value = true;
      } else {
        setTimeout(() => {
          cardList.value[cardOne.position].visible = false;
          cardList.value[cardTwo.position].visible = false;
          // Permitir al usuario voltear una nueva carta
          userCanFlipCard.value = true;
        }, 2000);
      }

      userSelection.value.length = 0;
    }
  },
  { deep: true }
);
</script>

<template>
  <NewGameButton />
  <GameBoard :cardList="cardList" :status="status" @flip-card="flipCard" />
</template>

<style>
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
}

html {
  /* background-image: url("/images/descarga.jpg");
  background-size: 100%;
  background-repeat: no-repeat; */
  background-color: #c1c2c6;
}

h1 {
  margin-top: 0;
}

a {
  color: white;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  color: #fff;
  padding: 0;
}

.status {
  font-family: "Titillium Web", sans-serif;
  font-size: 18px;
  text-transform: uppercase;
  color:black
}

.game-board {
  display: grid;
  grid-template-columns: repeat(6, 60px);
  grid-template-rows: repeat(3, 160px);
  grid-column-gap: 12px;
  grid-row-gap: 12px;
  justify-content: center;
}

@media screen and (min-width: 500px) {
  .game-board {
    grid-template-columns: repeat(6, 90px);
    grid-template-rows: repeat(3, 160px);
  }
}

@media screen and (min-width: 600px) {
  .game-board {
    grid-template-columns: repeat(6, 120px);
    grid-template-rows: repeat(3, 160px);
  }
}

.shuffle-card-move {
  transition: transform 0.8s ease-in;
}
</style>
