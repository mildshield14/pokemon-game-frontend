<template>
    <div class="game-container">
      <h2>Round: {{ currentRound }}</h2>
      <h3>Player {{ currentPlayer }}'s Turn</h3>
      <button class="attack" @click="performAttack(currentPlayer)">Attack</button>
      <div class="player-info">
        <div v-for="i in [1, 2]" :key="i" class="pokemon-details">
          <img :src="getPokemon(i).image" :alt="getPokemon(i).name" :class="{ 'rotate-img': i === 2 }" />
          <p class="pokemon-info">{{ getPokemon(i).name }}</p>
          <p class="pokemon-info pokemon-hp">HP: {{ getPokemon(i).hp }}</p>
        </div>
      </div>
      <h2 class="winner" v-if="winner">{{ winner }} wins!</h2>
      <button v-if="winner" @click="resetGame">Play Again</button>

    </div>
  </template>


  <script lang="ts">
  import {defineComponent, ref} from 'vue';
  export default defineComponent({
    name: 'Game',

    setup() {
      let view = ref<'player-selection' | 'pokemon-selection' | 'attack-selection' | 'player-selection' | 'player-attack-selection' | 'game'>('player-selection');

      const winner:any = ref(null);
      const attacks : any = {
        normal: {
          minDamage: 1,
          maxDamage: 10,
          chanceToHit: 100  // 100% chance to hit
        },
        special: {
          minDamage: 5,
          maxDamage: 15,
          chanceToHit: 80  // 80% chance to hit,ca veut dire special attacks can miss
        }
      };

      function calculateDamage(attackType: any) {
        const attack:any = attacks[attackType];
        if (Math.random() * 100 > attack.chanceToHit) {
          return 0; // attack missed
        }
        return Math.floor(Math.random() * (attack.maxDamage - attack.minDamage) + attack.minDamage);
      }


      const currentRound = ref(1);
      const maxRounds = 3;
      const currentPlayer = ref(1);
      let wait = 0;
      let turn = 0;

      function performAttack(attackerNumber:number) {
        const attacker = getPokemon(attackerNumber);
        const defenderNumber = attackerNumber === 1 ? 2 : 1;
        const defender = getPokemon(defenderNumber);
        const attacktype = localStorage.getItem("selectedPlayer" + attackerNumber.toString() + "Attack")
        const damage = calculateDamage(attacktype)
        const newDefenderHP = Math.max(defender.hp - damage, 0);  // histoire d'eviter les -ve

        // Check if the game should continue
        if (currentRound.value === maxRounds) {
          endGame(attackerNumber);
        } else if (attacktype === "special" && wait === 0) {
          wait = 1
          turn += 1
          currentPlayer.value = defenderNumber;
        } else {
          turn += 1
          if (turn % 2 == 0) {
            currentRound.value++;
          }
          const defenderElement = document.querySelector(`.pokemon-details:nth-child(${defenderNumber})`);
          if (defenderElement) {
            defenderElement.classList.add('attacked');
            console.log(defenderElement)
            setTimeout(() => {
              defenderElement.classList.remove('attacked');
            }, 1000);
          }
          updatePokemonHP(defenderNumber, newDefenderHP);

          console.log("turn " + turn)

          if (turn % 2 == 0) {
            currentRound.value++;
          }

          currentPlayer.value = defenderNumber;
        }
      }

      function getPokemon(playerNumber:number) {
        const pokemonData:any = localStorage.getItem(`selectedPokemonPlayer${playerNumber}`);
        return JSON.parse(pokemonData);
      }

      function updatePokemonHP(playerNumber:number, newHP:number) {
        const pokemon = getPokemon(playerNumber);
        pokemon.hp = newHP;
        localStorage.setItem(`selectedPokemonPlayer${playerNumber}`, JSON.stringify(pokemon));
      }

      function endGame(winningPlayerNumber:number) {
        alert(`Game Over! Player ${winningPlayerNumber} wins.`);
        winner.value = `Player ${winningPlayerNumber}`;
        const attack:HTMLElement = <HTMLElement>document.getElementsByClassName("attack")[0];
        attack.style.display = 'none'
        // reset local storage
      }

      function resetGame() {
        currentRound.value = 1;
        currentPlayer.value = 1;
        winner.value = null;
        const attack:HTMLElement = <HTMLElement>document.getElementsByClassName("attack")[0];
        attack.style.display = 'block'
      }
      return{
        currentRound,
        currentPlayer,
        performAttack,
        getPokemon,
        winner,
        resetGame
    }

    }

    });
  </script>


<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  max-width: 1000px;
  min-width: 500px;
  margin: 20px;
  padding: 20px;
  background: #f0f8ff;
  border-radius: 10px;
  box-shadow: 0 4px 8px black;
}

h2, h3 {
  color: #333;
  text-align: center;
}

.player-info {
  display: flex;
  justify-content: space-around;
  width: 100%;
}

.pokemon-details {
  text-align: center;
  margin: 10px;
}

.pokemon-details img {
  width: 100px;
  height: auto;
  display: block;
  margin: 0 auto;
}

.pokemon-info {
  color: #075c50;
  font-weight: bold;
}

.pokemon-hp{
  text-shadow: 2px 2px 8px #31c8ac;
}

.rotate-img{
    transform: rotateY(180deg);
}

@keyframes flicker {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.5; }
  }

  .attacked {
    animation: flicker 1s linear;
    text-shadow: 2px 2px 8px #870f0f;
  }

  .winner{
    text-shadow: 2px 2px 8px #870f0f;
  }

</style>
