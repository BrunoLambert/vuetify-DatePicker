<template>
  <v-layout row wrap>
    <v-flex xs12>
      <v-menu full-width :close-on-content-click="false" offset-y>
        <v-layout row wrap slot="activator">
          <v-flex xs6>
            <v-text-field
            slot="activator"
            label="Partida"
            v-model="dateFromFormatted"
            append-icon="chevron_right"
            readonly
            ></v-text-field>
          </v-flex>
          <v-flex xs6>
            <v-text-field
            slot="activator"
            label="Chegada"
            v-model="dateToFormatted"
            readonly
            ></v-text-field>
          </v-flex>
        </v-layout>
        <v-layout row wrap>
          <v-flex xs12 class="text-xs-center selecting_message_area">
            <v-alert type="info" :value="true" v-if="inputData == 'from'">Selecione a data de partida</v-alert>
            <v-alert type="info" :value="true" v-if="inputData == 'to'">Selecione a data de chegada</v-alert>
            <v-alert type="success" :value="true" v-if="inputData == 'finished'">Período selecionado! São {{ days }} dia{{ days > 1 ? 's' : '' }}!</v-alert>
          </v-flex>
          <v-flex xs12 sm6 md6>
            <v-btn :color="buttonColor ? buttonColor : 'primary'" fab flat small absolute left @click="updateMonthsDown">
              <v-icon>chevron_left</v-icon>
            </v-btn>
            <v-btn :color="buttonColor ? buttonColor : 'primary'" fab flat small absolute right hidden-sm-and-up @click="updateMonthsUp"><v-icon>chevron_right</v-icon></v-btn>
            <Calendar
            :calendarMinDate="!dateFrom || dateTo ? minDate : dateFrom"
            :date="dateFrom"
            :eventsColor="eventsColor"
            :month="calendarMonth.calendar1"
            :list="eventList"
            :language="calendarLanguage"
            @selected="selectedDate"
            ></Calendar>
          </v-flex>
          <v-flex xs12 sm6 md6 hidden-xs-only>
            <v-btn :color="buttonColor ? buttonColor : 'primary'" fab flat small absolute right @click="updateMonthsUp"><v-icon>chevron_right</v-icon></v-btn>
            <Calendar
            :calendarMinDate="!dateFrom || dateTo ? minDate : dateFrom"
            :date="dateTo"
            :month="calendarMonth.calendar2"
            :list="eventList"
            :eventsColor="eventsColor"
            :language="calendarLanguage"
            @selected="selectedDate"
            ></Calendar>
          </v-flex>
        </v-layout>
      </v-menu>
    </v-flex>
  </v-layout>
</template>

<script>
import Calendar from '~/components/Calendar.vue'
export default {
  components: { Calendar },
  props: [ 'daysToStart', 'buttonColor', 'eventsColor', 'calendarLanguage' ],
  data () {
    return {
      inputData: 'from',
      dateTo: '',
      dateFrom: '',
      minDate: null,
      calendarMonth: {
        calendar1: null,
        calendar2: null
      },
      eventList: []
    }
  },
  computed: {
    dateFromFormatted () {
      return this.dateFrom ? this.dateFrom.split('-').reverse().join('/') : null
    },
    dateToFormatted () {
      return this.dateTo ? this.dateTo.split('-').reverse().join('/') : null
    },
    days () {
      return ((new Date(this.dateTo) - new Date(this.dateFrom)) / 86400000) + 1
    }
  },
  watch: {
    dateFrom (value) {
      this.$emit('selectedDepartureDate', value)
    },
    dateTo (value) {
      this.$emit('selectedArrivalDate', value)
    }
  },
  methods: {
    selectedDate (value) {
      if (this.inputData === 'from') {
        this.inputData = 'to'
        this.dateFrom = value
        this.dateTo = null
        this.eventList = []
      } else if (this.inputData === 'to') {
        this.inputData = 'finished'
        this.dateTo = value
        this.eventList.push(value)
        this.calcRange()
      } else if (this.inputData === 'finished') {
        this.inputData = 'from'
        this.selectedDate(value)
      }
    },
    calcRange () {
      let range = { from: new Date(this.dateFrom), to: new Date(this.dateTo) }
      for (let start = range.from; range.from < range.to; start.setDate(start.getDate() + 1)) {
        this.eventList.push(start.toISOString().substr(0, 10))
      }
    },
    updateMonthsUp () {
      this.calendarMonth.calendar1 = this.calendarMonth.calendar2
      let nextMonth = parseInt(this.calendarMonth.calendar2.substr(5, 7)) + 1
      if (nextMonth > 12) {
        let year = parseInt(this.calendarMonth.calendar2.substr(0, 4)) + 1
        nextMonth = year + '-01'
      } else nextMonth = this.calendarMonth.calendar2.substr(0, 5) + nextMonth
      this.calendarMonth.calendar2 = nextMonth
    },
    updateMonthsDown () {
      this.calendarMonth.calendar2 = this.calendarMonth.calendar1
      let prevsMonth = parseInt(this.calendarMonth.calendar1.substr(5, 7)) - 1
      if (prevsMonth < 1) {
        let year = parseInt(this.calendarMonth.calendar1.substr(0, 4)) - 1
        prevsMonth = year + '-12'
      } else prevsMonth = this.calendarMonth.calendar1.substr(0, 5) + prevsMonth
      this.calendarMonth.calendar1 = prevsMonth
    }
  },
  created () {
    let now = new Date()
    let daysToStartInMS = null
    if (!this.daysToStart) daysToStartInMS = 0
    else daysToStartInMS = this.daysToStart * 86400000
    this.minDate = (new Date(Date.parse(now) + daysToStartInMS)).toISOString().substr(0, 10)
    this.calendarMonth.calendar2 = (new Date((new Date((new Date()).setMonth(now.getMonth() + 1))).setDate(1))).toISOString().substr(0, 7)
    this.calendarMonth.calendar1 = this.minDate.substr(0, 7)
  }
}
</script>

<style scoped>
.selecting_message {
  font-size: 150%;
  padding-top: 20px !important;
}
.selecting_message_area {
  background-color: white;
}
</style>
