<template>
    <div>
      <h1>API Data</h1>
      <h2>Choose a pokemon for player{{ selectedPlayer }}</h2>
      <div class="container-pokemon">

        <div class="pokemon-list">

          <div
              :class="['cards', { selected: pokemon.id===selectedCardId }]"
              @click="selectPokemon(pokemon)"
              v-for="pokemon in paginatedData" :key="pokemon.class">

            <div class="frontcard">
              <h2>{{ pokemon.name }}</h2>

            </div>
            <img :src="pokemon.image" :alt="pokemon.name" />
            <div class="backcard">
              <h2>{{ pokemon.name }}</h2>
              <p>Height: {{ pokemon.height }}</p>
              <p>Weight: {{ pokemon.weight }}</p>
              <p>HP: {{pokemon.hp}}</p>
              <br>
            </div>

          </div>

        </div>

      </div>

      <div class="pagination-controls">

        <button class="pokemonbutton" @click="previousPage" :disabled="current_page===1">
          Previous
        </button>
        <span>Page {{current_page}}</span>
        <button class="pokemonbutton" @click="nextPage" :disabled="current_page===total_page">
          Next
        </button>

      </div>

    </div>

  </template>

  <script lang="ts">
  import {computed, defineComponent, onMounted, ref} from 'vue';
  import axios from 'axios';
  export default defineComponent({
    name: 'Pokemon',

    setup() {
      const view = ref<'player-selection' | 'pokemon-selection'>('player-selection');
      const data = ref<any>([]);
      const current_page = ref(1);
      const items_per_page = 8;
      let selectedCardId=ref<number | null>(null);
      //let previouslySelectedElement: HTMLElement | null = null;
      const selectedPlayer = ref<number>(Number(localStorage.getItem('selectedPlayer')));
      const selectedPokemonPlayer1 = ref<any>(JSON.parse(<string>localStorage.getItem('selectedPokemonPlayer1')));
      const selectedPokemonPlayer2 = ref<any>(JSON.parse(<string>localStorage.getItem('selectedPokemonPlayer2')));
      const total_page=Math.ceil(50/items_per_page);
      //const router = useRouter();
      const fetchData = async () => {
        try {
          const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=50&offset=0');
          const all_pokemons = response.data.results;
          //  console.log(all_pokemons);

          for (const pokemon of all_pokemons) {
            const response_pokemon = await axios.get(pokemon.url);

            const id=response_pokemon.data.id;
            const img_url=response_pokemon.data.sprites.back_default;
            const height=response_pokemon.data.height;
            const weight=response_pokemon.data.weight;
            const hp=response_pokemon.data.stats[0].base_stat;

            data.value.push({
              id: id,
              name: pokemon.name,
              height: height,
              weight: weight,
              image: img_url,
              hp:hp
            });



          };

        } catch (error) {
          console.error('Error fetching data:', error);
        }
      };

      onMounted(() => {
        fetchData();
        const storedPokemon = localStorage.getItem(`selectedPokemonPlayer${selectedPlayer.value}`);
        if (storedPokemon) {
            const { id } = JSON.parse(storedPokemon);
            selectedCardId.value = id;
        }
      });

      const paginatedData = computed(() => {
        const start=(current_page.value-1) * items_per_page;
        const end=start+ items_per_page;
        return data.value.slice(start,end);
      })

      const nextPage = () => {
        if (current_page.value < total_page){
          current_page.value+=1;
        }

      }

      const previousPage = () => {
        if (current_page.value >1){
          current_page.value-=1;
        }
      }

      const selectPokemon = (pokemon: any) => {

        const storedPokemon = localStorage.getItem(`selectedPokemonPlayer${selectedPlayer.value}`);
        if (storedPokemon) {
            const { id } = JSON.parse(storedPokemon);
            selectedCardId.value = id;
        }

        if (selectedPlayer.value==1) {
          selectedPokemonPlayer1.value = pokemon;
          localStorage.setItem('selectedPokemonPlayer1', JSON.stringify(pokemon));
        }
        if (selectedPlayer.value==2){
          selectedPokemonPlayer2.value=pokemon;
          localStorage.setItem('selectedPokemonPlayer2', JSON.stringify(pokemon));
        }
      }

      const goBack = () => {
        console.log("player-selection")
        view.value = 'player-selection';
      }

      return {
        current_page,
        total_page,
        paginatedData,
        nextPage,
        previousPage,
        selectedPlayer,
        goBack,
        selectedCardId,
        selectPokemon};
    },

    methods: {
      saveData(event: any){

        let clicked_element : any = event.target;

        while (clicked_element.class!="cards"){
          console.log("BEfore: "+ clicked_element)
          clicked_element=clicked_element.parentNode;
          console.log("After: "+ clicked_element)
        };

        if (clicked_element!=null){}
        console.log(clicked_element);
        // need to search in array now about data then save attacks data specifically chosen
      }
    }
  });
  </script>

  <style>

  html{
    padding:20px;
    margin:0;
  }

  p{
    position: relative;
  }

  .cards{
    border:1px solid;
    position: relative;
    transition: transform 0.8s;
    transform-style: preserve-3d;
    margin-bottom: 20px;
    width: 200px;
    margin:20px;
  }

  .pokemon-list{
    text-align: center;
    display: flex;
    flex-wrap: wrap;
    flex:200px;
    padding-left:90px;
  }

  .container-pokemon{
  }


  .backcard{
    transform: rotateY(180deg);
    margin-bottom: 0;
  }

  .frontcard,  .backcard{
    backface-visibility: hidden;
    align-content: center;
    align-items: center;
  }

  .cards:hover{
    transform: rotateY(180deg);
  }

  .pagination{
    display: flex;
    justify-content: center;
    margin-top:20px;
    padding: 20px;
  }

  span{
    padding: 20px;
  }


  .pokemonbutton {
    padding: 10px 20px;
    border-radius: 5px;
    min-width: 100px;
    text-align: center;
  }

  .pokemonbutton:disabled {
    cursor: not-allowed;
  }



  .selected{
    border: 2px solid gold;
  }

  </style>
