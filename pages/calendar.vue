<template>
  <div class="mt-3">
    <v-row>
      <v-col class="py-0 text-medium" cols="6">
        {{ month }}
      </v-col>
      <v-col class="py-0 text-right" cols="6">
        <v-btn icon @click="onPrevMount">
          <v-icon>mdi-chevron-left</v-icon>
        </v-btn>
        <v-btn icon @click="onNextMount">
          <v-icon>mdi-chevron-right</v-icon>
        </v-btn>
      </v-col>
    </v-row>
    <full-calendar
      :ref="pRef"
      :editable="false"
      default-view="dayGridMonth"
      :plugins="calendarPlugins"
      :long-press-delay="0"
      :weekends="true"
      :selectable="false"
      :header="{
        left: '',
        right: ''
      }"
      :events="events"
      unselect-auto
      @eventRender="eventRender"
    />
  </div>
</template>

<script>
import dayGridPlugin from '@fullcalendar/daygrid'
import interactionPlugin from '@fullcalendar/interaction'
import tippy from 'tippy.js'
import 'tippy.js/dist/tippy.css' // optional for styling

export default {
  data () {
    return {
      pRef: 'eventCalendar',
      calendarPlugins: [dayGridPlugin, interactionPlugin],
      month: '',
      start: '',
      end: '',
      events: [],
      pId: 0,
      isEdit: false
    }
  },
  mounted () {
    this.month = this.$dayjs(this.$refs[this.pRef].getApi().getDate()).add(543, 'years').format('MMMM YYYY')
    this.fetchEvents()
  },
  methods: {
    async fetchEvents () {
      try {
        const events = await this.$axios.get('/events')
        this.events = events.data.data.map(d => ({
          title: d.title,
          description: d.description,
          start: this.$dayjs(d.start).format('YYYY-MM-DD'),
          end: this.$dayjs(d.end).format('YYYY-MM-DD'),
          id: d.id
        }))
      } catch (err) {
        this.$store.commit('alert/show', { type: 'error', message: err })
      }
    },
    onPrevMount () {
      this.$refs[this.pRef].getApi().prev()
      this.month = this.$dayjs(this.$refs[this.pRef].getApi().getDate()).add(543, 'years').format('MMMM YYYY')
    },
    onNextMount () {
      this.$refs[this.pRef].getApi().next()
      this.month = this.$dayjs(this.$refs[this.pRef].getApi().getDate()).add(543, 'years').format('MMMM YYYY')
      // const data = this.$refs[this.pRef].getApi().view
      // this.$emit('fetch-data', moment(data.activeStart), moment(data.activeEnd))
    },
    eventRender (elem) {
      tippy(elem.el, {
        content: elem.event.extendedProps.description
      })
    }
  }
}
</script>
