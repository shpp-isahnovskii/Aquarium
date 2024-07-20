<script setup>
import { reactive } from 'vue';
const emit = defineEmits(['createFish']);

const fishBodies = [
  'golden-purple-fish',
  'goldfish',
  'guppie',
  'tropical-fish',
  'tuna',
];

const fishNames = [
  'Lemongrass',
  'Flappy',
  'Kindly fish',
  'Robin',
  'Tomato',
  'Lego fish',
];

const getRandom = (arrayName) =>
  arrayName[Math.floor(Math.random() * arrayName.length)];

const state = reactive({
  name: getRandom(fishNames),
  body: getRandom(fishBodies),
});

const onSubmit = () => {
  if (!state.name) {
    return;
  }
  emit('createFish', { ...state, id: generateID() });
  resetForm();
};

const generateID = () => Math.random().toString(36);

const resetForm = () => {
  state.name = getRandom(fishNames);
  state.body = getRandom(fishBodies);
};
</script>
<template>
  <div class="w-96 bg-indigo-500">
    <form @submit.prevent="onSubmit" class="w-full px-2">
      <h3 class="text-white text-2xl mb-10 text-wrap font-semibold">
        Fish type
      </h3>
      <ul class="flex flex-wrap gap-4 mb-8">
        <li
          v-for="(body, index) in fishBodies"
          :key="index"
          class="w-20 h-20 block"
          @click="state.body = body"
        >
          <img
            :src="`/${body}.png`"
            :alt="body"
            :class="{ selected: body === state.body }"
          />
        </li>
      </ul>
      <h3 class="text-white text-2xl mb-2 text-wrap font-semibold">
        Fish name
      </h3>
      <input v-model="state.name" type="text" class="h-8 p-2 rounded-md mb-4" />
      <button
        type="submit"
        class="bg-red-500 text-white text-md px-6 py-2 rounded-md hover:bg-red-600 mx-auto"
      >
        Add Fish
      </button>
    </form>
  </div>
</template>

<style scoped>
.selected {
  filter: drop-shadow(2px 2px 0 #00cfff) drop-shadow(-2px -2px 0 #00cfff)
    drop-shadow(2px -2px 0 #00cfff) drop-shadow(-2px 2px 0 #00cfff);
}
</style>
