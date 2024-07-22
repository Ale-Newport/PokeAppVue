<template>
  <div class="p-8">
    <h1 class="text-4xl font-bold mb-6 mx-6 text-center">Lista de Pokémons</h1>
    <div class="flex flex-wrap justify-center mb-6 mx-6">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Buscar Pokémon"
        @input="filterPokemon"
        class="p-2 border rounded mb-6"
      />
      <select v-model="selectedType" @change="filterPokemonByType" class="p-2 border rounded mb-6 ml-4">
        <option value="">Todos los tipos</option>
        <option v-for="type in pokemonTypes" :key="type.name" :value="type.name">{{ type.name }}</option>
      </select>
    </div>
    <div v-if="loading" class="text-lg text-center">Loading...</div>
    <div v-if="!loading">
      <div class="flex flex-wrap justify-center">
        <PokemonCard
          v-for="pokemon in paginatedPokemon"
          :key="pokemon.name"
          :pokemon="pokemon"
          @click="showPokemonDetails(pokemon)"
        />
      </div>
      <div class="flex justify-center mt-6">
        <button
          @click="prevPage"
          :disabled="currentPage === 1"
          class="px-4 py-2 mx-1 border rounded"
        >
          Anterior
        </button>
        <span class="px-4 py-2 mx-1">Página {{ currentPage }} de {{ totalPages }}</span>
        <button
          @click="nextPage"
          :disabled="currentPage === totalPages"
          class="px-4 py-2 mx-1 border rounded"
        >
          Siguiente
        </button>
      </div>
    </div>
    <PokemonModal
      :show="showModal"
      :pokemon="selectedPokemon"
      @close="closeModal"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from 'vue';
import axios from 'axios';
import PokemonCard from './components/PokemonCard.vue';
import PokemonModal from './components/PokemonModal.vue';

const pokemonList = ref([]);
const filteredPokemonList = ref([]);
const searchQuery = ref('');
const selectedType = ref('');
const loading = ref(true);
const selectedPokemon = ref(null);
const showModal = ref(false);
const pokemonTypes = ref([]);

const itemsPerPage = 100;
const currentPage = ref(1);

const fetchPokemonDetails = async (pokemon) => {
  const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${pokemon.id}`);
  return response.data;
};

const fetchPokemonTypes = async () => {
  try {
    const response = await axios.get('https://pokeapi.co/api/v2/type');
    pokemonTypes.value = response.data.results;
  } catch (error) {
    console.error(error);
  }
};

const fetchPokemon = async () => {
  try {
    let response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=1000');
    pokemonList.value = await Promise.all(response.data.results.map(async (pokemon) => {
      const id = pokemon.url.split('/').filter(Boolean).pop();
      const details = await fetchPokemonDetails({ ...pokemon, id });
      return {
        ...pokemon,
        id: id,
        image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${id}.png`,
        details
      };
    }));
    filteredPokemonList.value = pokemonList.value;
  } catch (error) {
    console.error(error);
  } finally {
    loading.value = false;
  }
};

const filterPokemon = () => {
  let filtered = pokemonList.value.filter(pokemon =>
    pokemon.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
  if (selectedType.value) {
    filtered = filtered.filter(pokemon =>
      pokemon.details.types.some(type => type.type.name === selectedType.value)
    );
  }
  filteredPokemonList.value = filtered;
  currentPage.value = 1;
};

const filterPokemonByType = () => {
  filterPokemon();
};

const showPokemonDetails = async (pokemon) => {
  try {
    const details = await fetchPokemonDetails(pokemon);
    selectedPokemon.value = {
      ...pokemon,
      details,
    };
    showModal.value = true;
  } catch (error) {
    console.error(error);
  }
};

const closeModal = () => {
  showModal.value = false;
  selectedPokemon.value = null;
};

const paginatedPokemon = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return filteredPokemonList.value.slice(start, end);
});

const totalPages = computed(() => {
  return Math.ceil(filteredPokemonList.value.length / itemsPerPage);
});

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

onMounted(() => {
  fetchPokemon();
  fetchPokemonTypes();
});

watch([searchQuery, selectedType], filterPokemon);
</script>
