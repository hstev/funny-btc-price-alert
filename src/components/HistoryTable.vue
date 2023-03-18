<template>
  <v-table height="300px" fixed-header>
    <thead>
      <tr>
        <th class="text-left">
          Price
        </th>
        <th class="text-left">
          Time
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(item, index) in state.records"
        :key="index"
      >
        <td>{{ item.quotes.USD.price }}</td>
        <td>{{ item.last_updated }}</td>
      </tr>
      <tr v-if="state.records.length === 0">
        <td colspan="2" class="text-center">
          <v-icon>mdi-history</v-icon> Not history recorded
        </td>
      </tr>
    </tbody>
  </v-table>
</template>

<script setup>
import {reactive, watch} from 'vue'
 
const props = defineProps({
  record: Object
})

const state = reactive({
  records: []
})

watch(() => props.record, (newRecord, oldRecord) => {
  if ((oldRecord == null) || (newRecord.last_updated != oldRecord.last_updated)) {
    state.records.push(props.record)
  }
})

</script>
