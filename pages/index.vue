<template>
  <v-layout row wrap>
    <v-flex xs6>
      <BDatePicker
      :daysToStart="5"
      buttonColor="primary"
      eventsColor="primary"
      calendarLanguage="pt-BR"
      @selectedDepartureDate="newDepartureDate"
      @selectedArrivalDate="newArrivalDate"
      ></BDatePicker>
    </v-flex>
  </v-layout>
</template>

<script>
import BDatePicker from '~/components/bDatePicker.vue'
export default {
  components: { BDatePicker },
  methods: {
    newDepartureDate (value) {
    },
    newArrivalDate (value) {
    }
  }
}
</script>

<style lang="css">
</style>
