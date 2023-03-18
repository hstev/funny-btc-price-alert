<template>
    <v-sheet
      border="md"
      class="pa-6 text-white mx-auto"
      color="#141518"
      max-width="400"
    >
      <h4 class="text-h5 font-weight-bold mb-4">Chart coming soon</h4>
      <p>
        It will be a chart in this section
      </p>
    </v-sheet>
</template>

<script setup>
  import { reactive } from 'vue';

  const props = defineProps({
    record: Object
  })

  const state = reactive({
    series: [],
    chartOptions: {
      chart: {
        id: 'realtime',
        height: 350,
        type: 'line',
        animations: {
          enabled: true,
          easing: 'linear',
          dynamicAnimation: {
            speed: 1000
          }
        },
        toolbar: {
          show: false
        },
        zoom: {
          enabled: false
        }
      },
      dataLabels: {
        enabled: false
      },
      stroke: {
        curve: 'smooth'
      },
      title: {
        text: 'Dynamic Updating Chart',
        align: 'left'
      },
      markers: {
        size: 0
      },
      xaxis: {
        type: 'datetime',
        range: 1000,
      },
      yaxis: {
        max: 100
      },
      legend: {
        show: false
      },
    }
  })

</script>
