<template>
  <v-app>
    <v-container>
      <v-container>
        <v-text-field v-model="search" label="Pesquisar" placeholder="Bulbasaur" solo></v-text-field>
        <v-row>
          <v-col cols="2" v-for="pokemon in filtered_pokemons" :key="pokemon.name">
            <v-card @click="show_pokemon(get_id(pokemon))">
              <v-container>
                <v-row class="mx-0 d-flex vuetify-center">
                  <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(
                    pokemon
                  )}.png`" :alt="pokemon.name" width="100%" />
                </v-row>
                <h2 class="text-center">{{ show_name(pokemon) }}</h2>
              </v-container>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-container>
    <v-dialog v-model="show_dialog" width="800">
      <v-card v-if="selected_pokemon" class="px-4">
        <v-container>
          <v-row class="d-flex align-center">
            <v-col cols="4">
              <img
                :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selected_pokemon.id}.png`"
                :alt="selected_pokemon.name" width="80%" />
            </v-col>
            <v-col cols="8">
              <h1>{{ show_name(selected_pokemon) }}</h1>
              <v-chip v-for="type in selected_pokemon.types" :key="type.slot" class="mr-2">{{ type.type.name }}
              </v-chip>
              <v-divider class="my-4"></v-divider>
              <v-chip>Altura: {{ selected_pokemon.height * 2.54 }} cm</v-chip>
              <v-chip class="ml-2">Peso:
                {{ (selected_pokemon.weight * 0.453592).toFixed(0) }} kg</v-chip>
            </v-col>
          </v-row>
          <h2> Moves </h2>
        </v-container>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script>
import axios from "axios";
// import { response } from 'express';
export default {
  name: "App",

  components: {},

  data() {
    return {
      pokemons: [],
      search: "",
      show_dialog: false,
      selected_pokemon: null,
    };
  },

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=99999")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },
  methods: {
    get_id(pokemon) {
      return pokemon.url.split("/")[6];
    },
    show_name(pokemon) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    show_pokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selected_pokemon = response.data;
        this.show_dialog = !this.show_dialog;
      });
    },
  },
  computed: {
    filtered_pokemons() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search.toLowerCase());
      });
    },
  },
};
</script>
<style>
#app {
  background: linear-gradient(to bottom right,
      rgba(10, 10, 10, 1),
      rgba(12, 39, 63, 1)) no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}
</style>
