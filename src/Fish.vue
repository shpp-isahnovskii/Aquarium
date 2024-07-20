<script setup>
import { onMounted, onUnmounted, reactive, computed, ref } from 'vue';

const MIN_POSITION = 20; // % of the initial screen
const MAX_POSITION = 80; // % of the initial screen
const MIN_SPEED = 5000;
const MAX_SPEED = 10_000;
const FEED_TRIGGER = 50;
const DEATH_TRIGGER = 100;
const HUNGRY_INCREMENT = 0.5;
const LIFE_TICKER_INTERVAL = 50;

const props = defineProps({
  name: String,
  body: { type: String, default: 'tuna' },
  id: String,
});
const emit = defineEmits(['removeFish']);

const getRandomValue = (min = MIN_POSITION, max = MAX_POSITION) => {
  return Math.floor(Math.random() * (max - min + 1) + min);
};

const state = reactive({
  name: props.name,
  body: props.body,
  id: props.id,
  say: '',
  hungry: 0,
  alive: true,
  speed: getRandomValue(MIN_SPEED, MAX_SPEED),
  position: {
    x: 0,
    y: 0,
  },
  destination: {
    x: getRandomValue(),
    y: getRandomValue(),
  },
});

const headDirection = computed(() =>
  state.destination.x > state.position.x ? 1 : -1,
);

const healthBar = computed(
  () =>
    `rgb(255, ${255 - 255 * (state.hungry / 100)}, ${255 - 255 * (state.hungry / 100)})`,
);

let lifeTicker;

const moveFish = () => {
  state.position.x = state.destination.x;
  state.position.y = state.destination.y;

  state.destination.x = getRandomValue();
  state.destination.y = getRandomValue();

  state.speed = getRandomValue(MIN_SPEED, MAX_SPEED);
};

const setMovementTimer = () => {
  setTimeout(() => {
    if (!state.alive) {
      return;
    }
    setMovementTimer();
    moveFish();
  }, state.speed);
};

onMounted(() => {
  window.requestAnimationFrame(moveFish);
  setMovementTimer();
  //life engine
  lifeTicker = setInterval(() => {
    state.hungry = state.hungry + HUNGRY_INCREMENT;
    checkFeedTrigger();
    checkDeathTrigger();
  }, LIFE_TICKER_INTERVAL);
});

const checkFeedTrigger = () => {
  if (state.hungry <= FEED_TRIGGER) {
    return;
  }
  state.say = "it's time to feed";
};

const checkDeathTrigger = () => {
  if (state.hungry < DEATH_TRIGGER) {
    return;
  }
  clearInterval(lifeTicker);
  createDeadFish();
};

const feedClickHandler = () => {
  if (!state.alive) {
    emit('removeFish', state.id);
    return;
  }
  state.hungry = 0;
  state.say = '';
};

const createDeadFish = () => {
  state.body = 'dead';
  state.say = "I'm dead 👻";
  state.alive = false;
};

onUnmounted(() => {
  clearInterval(lifeTicker);
});
</script>

<template>
  <div
    ref="fishRef"
    class="absolute transition-all"
    :style="{
      top: `${state.destination.y}%`,
      left: `${state.destination.x}%`,
      transitionDuration: `${state.speed}ms`,
    }"
  >
    <figure class="relative z-50 block w-32 h-auto">
      <div
        v-if="state.say !== ''"
        class="absolute bg-white w-full h-8 -top-10 -right-3 rounded-full text-center content-evenly before:w-4 before:h-4 before:-z-10 before:bg-white before:absolute before:right-1/4 before:-bottom-2 before:translate-x-50 before:rotate-45"
      >
        {{ state.say }}
      </div>
      <div class="h-[76px] relative">
        <img
          class="absolute bottom-0"
          :src="`/${state.body}.png`"
          alt="fish icon"
          @click="feedClickHandler"
          :style="{ transform: `scaleX(${headDirection})` }"
        />
      </div>
      <figcaption>
        <div class="w-full text-white text-center bg-black/60">{{ name }}</div>
        <div
          class="h-1"
          :style="{
            width: `${state.hungry}%`,
            background: healthBar,
          }"
        />
      </figcaption>
    </figure>
  </div>
</template>
