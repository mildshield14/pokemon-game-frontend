<template>
  <main class="maincontainer">
    <!-- Player Selection View -->
    <Players
        v-if="view === 'player-selection'"
        @selectPlayer="selectPlayer"
    />

    <!-- Choose Attack Button  -->
    <button
        class="chooseattack"
        @click="chooseattack"
        v-if="view === 'player-selection'">
      Choose Attack
    </button>

    <!-- Back Button for Pokemon Selection -->
    <button
        class="backbutton"
        @click="goBack"
        v-if="view === 'pokemon-selection'">
      Back to Player Selection
    </button>

    <!-- Pokemon Selection View -->
    <PokemonsDisplay
        v-if="view === 'pokemon-selection'"
    />

    <!-- Player Attack Selection View -->
    <Players
        v-if="view === 'player-attack-selection'"
        @click="goAttack"
        @selectPlayer="selectPlayerAttack"
    />

    <!-- Attack Selection View -->
    <Attacks
        v-if="view === 'attack-selection'"
    />

    <!-- Back Button for Attack Selection -->
    <button
        class="backattackbutton"
        @click="goBackAttack"
        v-if="view === 'attack-selection'">
      Back
    </button>

    <!-- Start Game Button -->
    <button
        class="startgame"
        @click="startGame"
        v-if="view === 'player-attack-selection'">
      Start Battle
    </button>

    <!-- Game View -->
    <Game
        v-if="view === 'game'"
    />
    <button v-if="view==='game'" @click="home">Go Back Home</button>

  </main>
</template>


<script setup lang="ts">
import { ref } from 'vue';

import Players from './components/Players.vue';
import PokemonsDisplay from './components/PokemonsDisplay.vue';
import Game from './components/Game.vue'
import Attacks from "./components/Attacks.vue";


const view = ref<'player-selection' | 'pokemon-selection' | 'attack-selection' | 'player-selection' | 'player-attack-selection'|'game'>('player-selection');
const selectedPlayer = ref<number | null>(null);
const selectedPlayerAttack = ref<number | null>(null);

async function selectPlayer(player: number)
{
  console.log("selected player" + player)
  selectedPlayer.value = player;

  view.value = 'pokemon-selection';

  //await axios.post('http://localhost:3001/api/save-turn', player);
  localStorage.setItem("selectedPlayer",player.toString());
}


function selectPlayerAttack(player: number) {
  selectedPlayerAttack.value = player;
  console.log("chosen here");
  view.value = 'attack-selection';
  localStorage.setItem("selectedPlayer",player.toString());
}

function goBack() {
  view.value = 'player-selection';
  selectedPlayer.value = null;
}

function goBackAttack() {
  view.value = 'player-attack-selection';
  selectedPlayer.value = null;
}


function goAttack() {
  view.value = 'attack-selection';
  selectedPlayer.value = null;
}

function chooseattack(){

  if (localStorage.getItem("selectedPokemonPlayer2")!=null
      && localStorage.getItem('selectedPokemonPlayer1') !=null){
    alert("You will now choose your attacks")
    view.value='player-attack-selection';

  }else{
    alert("All players should select a pokemon")
  }

}

function home() {
  view.value = 'player-selection';
  console.log('view changed to:', view.value);
  localStorage.clear();
}

function startGame() {
  if (localStorage.getItem("selectedPlayer1Attack")!=null
      && localStorage.getItem('selectedPlayer2Attack') !=null){
    alert("Game is about to start")
    view.value='game';

  }else{
    alert("All players should select an attack first")
  }
}



</script>

<style>

.maincontainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  border:1px solid wheat;
  padding: 20px;
}

button {
  background: linear-gradient(to bottom right, #56887D, #2c3e50);
  border: none;
  border-radius: 12px;
  color: white;
  cursor: pointer;
  font-size: 16px;
  font-weight: 500;
  margin: 10px;
  padding: 10px 20px;
  text-align: center;
  transition: box-shadow .2s ease-in-out, transform .2s;
}

button:hover {
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  transform: translateY(-2px);
}

.chooseattack, .startgame {
  font-size: 18px;
  padding: 15px 30px;
}

.backbutton, .backpokemonbutton, .backattackbutton {
  font-size: 14px;
  padding: 8px 16px;
}




</style>
