<template>
    <v-card
        :loading="state.isUpdating"
        color="align-center mb-6"
        class="mx-auto mt-5"
        max-width="400"
    >
        <template v-slot:loader="{ isActive }">
            <v-progress-linear
                :active="isActive"
                color="white"
                height="4"
                indeterminate
            ></v-progress-linear>
        </template>
        <v-card-title>
          <v-row class="pa-5">
                <v-row>
                  <v-col class="text-center">
                      <h2 class="text-h2 font-weight-bold">{{ computedPrice }}</h2>
                      <span class="text-h5">{{ state.coin }}</span>
                  </v-col>
                </v-row>
            </v-row>
        </v-card-title>
        <v-img
        cover
        height="auto"
        :src="state.imageStatus"
        >
            
        </v-img>


        <v-divider></v-divider>

        <v-card-actions>
        <v-switch
            v-model="state.autoUpdate"
            :disabled="state.isUpdating"
            class="mt-0 ms-2"
            color="green-lighten-2"
            density="compact"
            hide-details
            label="Auto update"
            @click="autoUpdate"
        ></v-switch>

        <v-spacer></v-spacer>

        <v-btn
            :disabled="state.autoUpdate"
            :loading="state.isUpdating"
            :variant="state.isUpdating ? 'tonal' : undefined"
            color="blue-grey-lighten-3"
            prepend-icon="mdi-update"
            @click="updateNow"
        >
            Update Now
        </v-btn>
    
        </v-card-actions>
    </v-card>
    <p class="text-center mt-n5" v-if="state.timer > 0">{{ state.timer }} seconds to auto-update</p> 
</template>

<script setup>
import { reactive, watch, computed } from 'vue';

import imgUp from '../assets/images/up.png'
import imgDown from '../assets/images/down.jpg'
import imgConfused from '../assets/images/confused.jpg'

import downSound_1 from '@/assets/sounds/downSound_1.mp3'
import downSound_2 from '@/assets/sounds/downSound_2.mp3'
import downSound_3 from '@/assets/sounds/downSound_3.mp3'
import downSound_4 from '@/assets/sounds/downSound_4.mp3'
import downSound_5 from '@/assets/sounds/downSound_5.mp3'
import upSound_1 from '@/assets/sounds/upSound_1.mp3'
import upSound_2 from '@/assets/sounds/upSound_2.mp3'
import upSound_3 from '@/assets/sounds/upSound_3.mp3'
import upSound_4 from '@/assets/sounds/upSound_4.mp3'
import upSound_5 from '@/assets/sounds/upSound_5.mp3'
import noMoveSound from '@/assets/sounds/noMoveSound.mp3'

const emit = defineEmits(['updateNow'])

const images = {
  up: imgUp,
  down: imgDown,
  confused: imgConfused
}

const sounds = {
  down: [downSound_1,downSound_2,downSound_3,downSound_4,downSound_5],
  up: [upSound_1,upSound_2,upSound_3,upSound_4,upSound_5],
  equal: noMoveSound
}

const state = reactive({
  autoUpdate: false,
  isUpdating: false,
  price: null,
  coin: 'BTC',
  imageStatus: images.confused,
  lastUpdated: null,
  timer: 0,
  intervals: [],
  audio: null
})



const playSound = status => {
  let sound;
  if (status == "success") {
    sound = sounds.up[ Math.floor(Math.random() * sounds.up.length)];
  }
  else if (status == "failure") {
    sound = sounds.down[ Math.floor(Math.random() * sounds.up.length)];
  }
  else {
    sound = sounds.equal;
  }
  state.audio = new Audio(sound);
  state.audio.play();
}

const updateNow = async () => {
  state.isUpdating = true;
  await fetch('https://api.coinpaprika.com/v1/tickers/btc-bitcoin')
  .then(res => res.json())
  .then(res => {
    state.price = res.quotes.USD.price
    state.lastUpdated = res.last_updated
    emit('updateNow', res);
  })
  .catch(err => console.error(err))
  state.isUpdating = false;
}

const autoUpdate = async () => {
  if (!state.autoUpdate) {
    state.timer = 150;
    state.intervals.push(setInterval(() => {
      state.timer--;
      if (state.timer == 0) {
        state.timer = 150;
      }
    }, 1000));
    state.intervals.push(setInterval(async () => {
      await updateNow();
    }, 150000));
  }
}

const stopAutoUpdate = () => {
    state.timer = 0;
    state.intervals.forEach(element => {
      clearInterval(element);
    });
    console.log(state.intervals)
    state.intervals = [];
}

watch(() => state.price, async (newPrice, oldPrice) => {
  let soundStatus = '';
  if (newPrice > oldPrice) {
    state.imageStatus = images.up;
    soundStatus = 'success';
  }
  if (newPrice < oldPrice) {
    state.imageStatus = images.down;
    soundStatus = 'failure';
  }
  playSound(soundStatus);
  //logTable(state.price, soundStatus == 'success')
})

watch (() => state.autoUpdate, async (newCheck, oldChceck) => {
    if (!newCheck) {
        stopAutoUpdate();
    }
})

const computedPrice = computed(() => {
  const textValue = Number(state.price).toFixed(2).toLocaleString().replace('.','.');
  return textValue != "NaN" ? textValue : '-';
})

</script>