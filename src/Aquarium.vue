<script setup>
import Fish from '@/Fish.vue';

defineProps(['allFish']);
defineEmits(['removeFish']);
</script>

<template>
  <div ref="aquarium" class="bg-[url('/bg.jpg')] w-full overflow-hidden">
    <Fish
      v-for="fish in allFish"
      :key="fish.id"
      v-bind="fish"
      @remove-fish="$emit('removeFish', fish.id)"
    />
  </div>
</template>
