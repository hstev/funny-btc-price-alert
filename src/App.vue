<template>
  <v-app>
    <v-main>
      <v-card
        :loading="state.isUpdating"
        color="blue-grey-darken-1"
        class="mx-auto mt-5"
        max-width="420"
      >
        <template v-slot:loader="{ isActive }">
          <v-progress-linear
            :active="isActive"
            color="green-lighten-3"
            height="4"
            indeterminate
          ></v-progress-linear>
        </template>

        <v-img
          cover
          height="200"
          :src="state.imageStatus"
        >
          <v-row class="pa-5">
            <v-col cols="12">
              <v-menu
                location="bottom start"
                origin="overlap"
                transition="slide-y-transition"
              >
                <template v-slot:activator="{ props }">
                  <v-btn
                    v-bind="props"
                    icon="mdi-dots-vertical"
                    density="comfortable"
                    variant="tonal"
                  ></v-btn>
                </template>

                <v-list :lines="false">
                  <v-list-item
                    title="Update"
                    @click="updateNow"
                  ></v-list-item>
                </v-list>
              </v-menu>
            </v-col>

            <v-row>
              <v-col class="text-center">
                <h3 class="text-h5">{{ computedPrice }}</h3>
                <span color="red">{{ state.coin }}</span>
              </v-col>
            </v-row>
          </v-row>
        </v-img>

        <v-container>
          <v-row dense>
            <v-col cols="12">
              <v-text-field
                disabled
                color="blue-grey-lighten-2"
                label="Last updated"
                v-model="state.lastUpdated"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-container>

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
    </v-main>
  </v-app>
</template>

<script setup>
import {reactive, onMounted, computed, watch} from 'vue'
import imgUp from './assets/images/up.png'
import imgDown from './assets/images/down.jpg'
import imgConfused from './assets/images/confused.jpg'
import downSound_1 from './assets/sounds/downSound_1.mp3'
import downSound_2 from './assets/sounds/downSound_2.mp3'
import downSound_3 from './assets/sounds/downSound_3.mp3'
import downSound_4 from './assets/sounds/downSound_4.mp3'
import downSound_5 from './assets/sounds/downSound_5.mp3'
import upSound_1 from './assets/sounds/upSound_1.mp3'
import upSound_2 from './assets/sounds/upSound_2.mp3'
import upSound_3 from './assets/sounds/upSound_3.mp3'
import upSound_4 from './assets/sounds/upSound_4.mp3'
import upSound_5 from './assets/sounds/upSound_5.mp3'
import noMoveSound from './assets/sounds/noMoveSound.mp3'

function playSound(status) {
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
  const audio = new Audio(sound);
  audio.play();
}

const sounds = {
  down: [downSound_1,downSound_2,downSound_3,downSound_4,downSound_5],
  up: [upSound_1,upSound_2,upSound_3,upSound_4,upSound_5],
  equal: noMoveSound
}

const images = {
  up: imgUp,
  down: imgDown,
  confused: imgConfused
}

const state = reactive({
  autoUpdate: false,
  isUpdating: false,
  price: null,
  coin: 'BTC',
  imageStatus: images.confused,
  lastUpdated: null
})

const autoUpdate = async () => {
  setInterval(() => {
    updateNow();
  }, 150000);
}

const updateNow = async () => {
  state.isUpdating = true;
  await fetch('https://api.coinpaprika.com/v1/tickers/btc-bitcoin')
  .then(res => res.json())
  .then(res => {
    state.price = res.quotes.USD.price
    state.lastUpdated = res.last_updated
  })
  .catch(err => console.error(err))
  state.isUpdating = false;
}

const computedPrice = computed(() => {
  return "$ " + parseFloat(state.price).toLocaleString()
})

watch(() => state.price, async (newPrice, oldPrice) => {
  if (newPrice > oldPrice) {
    state.imageStatus = images.up;
    playSound('success');
  }
  if (newPrice < oldPrice) {
    state.imageStatus = images.down;
    playSound('failure');
  }
  if (newPrice == oldPrice) {
    state.imageStatus = images.confused;
    playSound('nomove');
  }
})

onMounted(() => {
  updateNow();
})
</script>
