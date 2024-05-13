<template>
    <form>
      <div class="container-home">
        <div class="container jumbotron">
          <h1>Welcome to Pokémon Challenge</h1>
          <p>Choose a player</p>
        </div>
        <button class="playerhome" @click.prevent="selectPlayer(1)">Player 1</button>
        <button class="playerhome" @click.prevent="selectPlayer(2)">Player 2</button>
      </div>
    </form>
  </template>
  
  <script setup lang="ts">
  import { defineEmits } from 'vue';
  
  const emit = defineEmits(['selectPlayer']);
  
  function selectPlayer(player: number) {
    console.log("player"+player)
    emit('selectPlayer', player);
  }
  </script>
  
  <style>
  .playerhome {
    background-color: gray;
    width: 120px;
    padding: 40px;
    margin-right: 20px;
    cursor: pointer;
  }
  
  button{
    border-radius: 5px;
  }
  
  .container-home {
    display: flex;
    padding: 20px;
  }
  
  .jumbotron {
    padding: 20px;
    text-align: center;
  }
  
  .playerhome:hover {
    border: 2px solid gold;
  }
  </style>
  