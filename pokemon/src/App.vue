<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <div class="container">
    <h1>Liste des Pokémon</h1>
    <section>
      <div v-for="(pokemon, index) in pokemonList" :key="index" class="card">
        <div class="col-3">
          <h2>{{ pokemon.name }}</h2>
          <div class="img_card">
            <img :src="pokemon.image" :alt="pokemon.name" />
          </div>
          <p>Caractéristiques : {{ pokemon.characteristics.join(', ') }}</p>
          <div class="pokemon_stats">
            <p class="pokemon-hp">HP <br> {{ pokemon.stats.base_stat }} </p>
            <p class="pokemon-attack">ATQ <br> {{ pokemon.stats.base_stat }}</p>
            <p class="pokemon-defense"> DEF <br> {{ pokemon.stats.base_stat }}</p>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemonList: [],
    };
  },
  created() {
    this.fetchPokemonList();
  },
  methods: {
    async fetchPokemonList() {
      try {
        const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=20');
        const data = await response.json();
        await Promise.all(
          data.results.map(async (pokemon) => {
            const pokemonData = await this.fetchPokemonData(pokemon.url);
            this.pokemonList.push({
              name: pokemonData.name,
              characteristics: pokemonData.abilities.map((ability) => ability.ability.name),
              image: pokemonData.sprites.front_default,
              stats: pokemonData.stats.find((stat) => stat.stat.name),
            });
          })
        );
      } catch (error) {
        console.error('Une erreur s\'est produite :', error);
      }
    },
    async fetchPokemonData(url) {
      const response = await fetch(url);
      return await response.json();
    },
  },
};
</script>
