<script setup>
import { ref } from 'vue';
import Aquarium from '@/Aquarium.vue';
import FishForm from '@/FishForm.vue';

const allFish = ref([{ name: 'The First Fish', body: 'tuna', id: '0' }]);

const addFish = (fish) => {
  allFish.value.push(fish);
};
const removeFish = (id) => {
  allFish.value = allFish.value.filter((fish) => fish.id !== id);
};
</script>
<template>
  <FishForm @create-fish="addFish" />
  <Aquarium :allFish="allFish" @removeFish="removeFish" />
</template>
