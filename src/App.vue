<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <Notice />
        <v-row>
          <v-col cols="12" md="4" offset="2" class="mx-auto">
            <CurrencyDetail @updateNow="logRecord"/>
          </v-col>
          <v-col cols="12" md="4" class="mx-auto mt-5" >
              <Chart />
              <br />
              <HistoryTable :record="state.record"/>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import {reactive} from 'vue';

import Chart from '@/components/Chart.vue'
import HistoryTable from '@/components/HistoryTable.vue'
import CurrencyDetail from '@/components/CurrencyDetail.vue'
import Notice from '@/components/Notice.vue'

const state = reactive({
  record: null
})

const logRecord = (record) => {
  state.record = record
}
</script>
