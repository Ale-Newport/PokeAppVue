<template>
  <div v-if="show" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center" @click="closeModal">
    <div class="bg-white p-6 rounded-lg shadow-lg max-w-lg w-full relative dark:bg-gray-800 dark:text-white" @click.stop>
      <button class="absolute top-2 right-2 text-lg font-bold" @click="closeModal">X</button>
      <div v-if="pokemon">
        <h2 class="text-2xl font-bold capitalize mb-4">{{ pokemon.name }}</h2>
        <img :src="pokemon.image" :alt="pokemon.name" class="w-32 h-32 mx-auto mb-4" />
        <ul class="text-left">
          <li><strong>Height:</strong> {{ pokemon.details.height }}</li>
          <li><strong>Weight:</strong> {{ pokemon.details.weight }}</li>
          <li><strong>Base Experience:</strong> {{ pokemon.details.base_experience }}</li>
          <li><strong>Types:</strong>
            <ul>
              <li v-for="type in pokemon.details.types" :key="type.type.name">
                {{ type.type.name }}
              </li>
            </ul>
          </li>
          <li><strong>Abilities:</strong>
            <ul>
              <li v-for="ability in pokemon.details.abilities" :key="ability.ability.name">
                {{ ability.ability.name }}
              </li>
            </ul>
          </li>
          <li><strong>Stats:</strong>
            <ul>
              <li v-for="stat in pokemon.details.stats" :key="stat.stat.name">
                {{ stat.stat.name }}: {{ stat.base_stat }}
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { } from 'vue';

const props = defineProps({
  show: Boolean,
  pokemon: Object,
});

const emit = defineEmits(['close']);

const closeModal = () => {
  emit('close');
};
</script>
