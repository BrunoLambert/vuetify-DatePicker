<template>
  <v-date-picker
  full-width
  v-model="vueDate"
  no-title
  :min="calendarMinDate"
  :picker-date="month"
  :locale="language"
  :prev-icon="''"
  :next-icon="''"
  :event-color="eventsColor ? eventsColor : 'primary'"
  :events="list"
  @input="selectedDate">
  </v-date-picker>
</template>

<script>
export default {
  props: [ 'calendarMinDate', 'month', 'list', 'eventsColor', 'language' ],
  data () {
    return {
      vueDate: null
    }
  },
  methods: {
    selectedDate (value) {
      this.$emit('selected', value)
      this.vueDate = null
    }
  }
}
</script>

<style lang="css">
</style>
