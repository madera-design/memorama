<script setup>
import { storeToRefs } from "pinia";
import { useGameStore } from "../stores/GameStore";

const gameStore = useGameStore();
const { newPlayer } = storeToRefs(gameStore);

const startNewGame = () => {
  if (newPlayer.value) {
    gameStore.startGame();
  } else {
    gameStore.restartGame();
  }
};
</script>

<template>
  <button v-if="newPlayer" @click="startNewGame" :class="$style.button">
    JUGAR !
  </button>
  <button v-else @click="startNewGame" :class="$style.button">
    VOLVER A JUGAR
  </button>
</template>

<style module>
.button {
  background-color: #e78805;
  color: white;
  padding: 8px 16px 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 15px auto 15px;
  font-weight: bold;
  font-family: "Titillium Web", sans-serif;
  font-weight: bold;
  font-size: 1rem;
  border: 0;
  border-radius: 10px;
  transition: 0.2s all ease-in;
  border: 2px solid #e78805;
  cursor: pointer;
}
</style>
