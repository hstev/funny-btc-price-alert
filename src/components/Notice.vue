<template>
    <v-dialog
      v-model="dialog"
      persistent
      width="auto"
    >
      <v-card>
        <v-card-title class="text-h5">
          
          <v-icon color="warning">mdi-alert</v-icon>  Sound alerts spoiler
        </v-card-title>
        <v-card-text>
            This project consume a third part API and show images, play sounds and collect data to show a chart.
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="green-darken-1"
            variant="text"
            @click="goHome"
          >
            Disagree
          </v-btn>
          <v-btn
            color="green-darken-1"
            variant="text"
            @click="dialog = false"
          >
            Agree
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>

<script setup>
    import { ref } from 'vue';
    const dialog = ref(true)
    
    const goHome = () => {
        window.location.href = 'https://hstev.github.io'
    }
</script>