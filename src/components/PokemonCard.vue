<template>
  <div
    class="pokemon-card border rounded-lg shadow-lg p-4 m-4 text-center w-40 cursor-pointer transform transition-transform hover:scale-105 dark:bg-gray-800 dark:border-gray-600"
    @click="$emit('click', pokemon)"
  >
    <img :src="pokemon.image" :alt="pokemon.name" class="w-24 h-24 mx-auto" />
    <p class="capitalize mt-2 dark:text-white">{{ pokemon.name }}</p>
  </div>
</template>

<script setup>
import { } from 'vue';

const props = defineProps({
  pokemon: Object,
});
</script>

<style scoped>
.pokemon-card:hover {
  transform: scale(1.05);
}
</style>
