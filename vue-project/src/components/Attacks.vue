<template>
    <div>
      <h2>Choose an Attack Type</h2>
      <div class="attack-type-buttons">
        <button
          :class="{ selected: selectedAttackType === 'normal' }"
          @click="setAttackType('normal')">
          Normal Attack
        </button>
        <button
          :class="{ selected: selectedAttackType === 'special' }"
          @click="setAttackType('special')">
          Special Attack
        </button>
      </div>
    </div>
  </template>


<script lang="ts">
import {defineComponent} from "vue";
import {ref, onMounted} from 'vue';
export default defineComponent({
  name: 'Attack',

  setup() {


    const view = ref('player-selection');
    const selectedPlayer = ref(Number(localStorage.getItem('selectedPlayer')));
    const selectedAttackType:any = ref(null);

    function setAttackType(attackType:any) {
      console.log("Selected attack type:", attackType);
      selectedAttackType.value = attackType;

      view.value = 'player-attack-selection';

      localStorage.setItem(`selectedPlayer${selectedPlayer.value.toString()}Attack`, attackType);
    }

    onMounted(() => {
      const storedAttackType = localStorage.getItem(`selectedPlayer${selectedPlayer.value.toString()}Attack`);
      if (storedAttackType) {
        selectedAttackType.value = storedAttackType;
      }
    });

    return {
      setAttackType,
      selectedAttackType,
    }

  }
});
</script>

<style>
.attack-type-buttons button {
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
}

.selected {
  border: 1px solid gold;
}

</style>
