<template>
  <div class="section">
    <div class="columns is-multiline is-centered">
      <div class="column is-6-desktop is-10-tablet is-12-mobile">
        <!-- pokemon card >> -->
        <PokemonCard 
          v-bind:name="this.result.name"
          v-bind:imgs="this.result.imgs"
          v-bind:height="this.result.height"
          v-bind:weight="this.result.weight"
          v-bind:abilities="this.result.abilities"
          v-bind:types="this.result.types"
        ></PokemonCard>
        <!-- << pokemon card -->
            <!-- main component >> -->
        <div class="section">
          <h1 class="title is-4">Welcome to Pokémon Explorer!</h1>
          <div class="level">
            <div class="level-left">
              <div class="level-item">
                <label for="search">Look for a Pokémon:</label>
              </div>
              <div class="level-item">
                <b-input
                  id="search"
                  v-model="term"
                  @keyup.native.enter="searchPokemon"
                  @input="updateSuggestionsList"
                />
              </div>
              <div class="level-item">
                <b-button type="is-primary" @click.prevent="searchPokemon">Search</b-button>
              </div>
            </div>
          </div>
          <b-loading :is-full-page="true" :active.sync="isLoading" :can-cancel="true"></b-loading>
          <div class="container">
            <div class="columns is-multiline is-mobile is-gapless">
              <div
                class="column is-3-desktop is-3-tablet is-6-mobile"
                v-for="pokemon in limitedFilteredPokemonList"
                :key="pokemon.id"
              >
                <a
                  class="is-size-7"
                  @click.prevent="transferSelectedSuggestion(pokemon.name)"
                >{{ pokemon.name }}</a>
              </div>
            </div>
          </div>
        </div>
        <!-- << main component -->
         <!--  footer >> -->
        <div class="container">
          <p class="has-text-centered is-size-7">powered by <a href="https://pokeapi.co/" target="_blank">PokéAPI</a> and <a href="https://vuejs.org/" target="_blank">Vue</a>, developed by <a href="https://philot.space/" target="_blank">Philot</a></p>
        </div>
        <!-- << footer -->
      </div>
    </div>
  </div>
</template>

<script>
import PokemonCard from "./PokemonCard";

export default {
  name: "Main",
  components: {
    PokemonCard
  },
  props: {

  },
  data() {
    return {
      term: "wobbuffet",
      pokemonList: [],
      filteredPokemonList: [],
      result: {
        name: String,
        imgs: [],
        height: Number,
        weight: Number,
        abilities: [],
        types: []
      },
      isLoading: true
    };
  },

  computed: {
    getTerm() {
      return this.term;
    },
    limitedFilteredPokemonList() {
        return this.filteredPokemonList.slice(0, 20);
    }
  },

  methods: {
    populateSuggestionsList() {
      this.isLoading = true;
      fetch(`https://pokeapi.co/api/v2/pokemon?limit=999`)
        .then(response => {
          if (response.status !== 200) {
            console.log(
              `we have a problem with status code ${response.status}`
            );
            return;
          }
          response.json().then(data => {
            //returns pokemons and maps it to our component data
            [...data.results].map(result =>
              this.pokemonList.push({
                name: result.name,
                id: result.url.split("/").reverse()[1]
              })
            );
            this.filteredPokemonList = [...this.pokemonList];
            this.isLoading = false;
            // console.log(this.pokemonList);
          });
        })
        .catch(err => console.error(err));
    },

    updateSuggestionsList() {
      const self = this;
      const regexp = new RegExp(this.getTerm, "i");
      this.filteredPokemonList = this.pokemonList.filter(item =>
        item.name.includes(this.getTerm)
      );
    },

    transferSelectedSuggestion(term) {
      // console.log(term);
      const self = this;
      this.term = term;
      this.searchPokemon();
    },

    searchPokemon() {
      if(screen.width <= 768) {
        window.scrollTo(0,0);
      }
      this.isLoading = true;
      const self = this;
      //if empty, do nothing
      if (!this.getTerm) {
        //   console.log('nothing');
        this.isLoading = false;
        return;
      }
      //if there's a term, fetch the api
      fetch(`https://pokeapi.co/api/v2/pokemon/${this.getTerm}`)
        .then(response => {
          if (response.status !== 200) {
            console.log(
              `we have a problem with status code ${response.status}`
            );
            this.isLoading = false;
            this.$buefy.dialog.alert("No Pokémon found!");
            return;
          }
          response.json().then(data => {
            // console.log(data);
            this.result.name = data.name;
            //cherry picking which images I want to use
            let pickedImgs = [
              data.sprites.back_default,
              data.sprites.front_default
            ]
            //removing null items
            this.result.imgs = pickedImgs.filter(x => x);
            this.result.height = data.height;
            this.result.weight = data.weight;
            this.result.abilities = data.abilities;
            this.result.types = data.types;
            this.isLoading = false;
          });
        })
        .catch(err => console.error(err));
    }
  },

  mounted() {
    this.populateSuggestionsList();
    this.searchPokemon();
  }
};
</script>