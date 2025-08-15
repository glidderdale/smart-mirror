<template>
  <div class="calendar-widget">
    <h2 class="day-of-week">Upcoming</h2>
    <hr class="divider"></hr>
    <div v-if="loading">Loading events...</div>
    <div v-else-if="events.length === 0">No upcoming events.</div>
    <div v-else>
      <h3 class="event" v-for="event in events" :key="event.id">
        {{ formatEventDate(event.start) }} {{ formatEventTime(event.start) }} - {{ event.summary }}
      </h3>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CalendarWidget',
  data() {
    return {
      events: [],
      loading: true
    };
  },
  mounted() {
    this.fetchEvents();
  },
  methods: {
    async fetchEvents() {
      this.loading = true;
      try {
        const response = await fetch('https://v1.nocodeapi.com/glidderdale/calendar/WSCsbfUlGEhJmrlv/listEvents?calendarId=cre7s05too9circnedc7cftpi4@group.calendar.google.com&maxResults=5');
        const data = await response.json();
        this.events = (data.items || []).filter(e => e.start && e.summary);
      } catch (e) {
        this.events = [];
      }
      this.loading = false;
    },
    formatEventDate(start) {
      let dateObj;
      if (start.dateTime) {
        dateObj = new Date(start.dateTime);
      } else if (start.date) {
        dateObj = new Date(start.date);
      } else {
        return '';
      }
      // Format as e.g. Mon 8/19
      const options = { weekday: 'short', month: 'numeric', day: 'numeric' };
      return dateObj.toLocaleDateString(undefined, options);
    },
    formatEventTime(start) {
      // Handles both dateTime and date (all-day)
      if (start.dateTime) {
        const date = new Date(start.dateTime);
        let hours = date.getHours();
        const minutes = date.getMinutes();
        const ampm = hours >= 12 ? 'pm' : 'am';
        hours = hours % 12;
        hours = hours ? hours : 12;
        const minutesStr = minutes < 10 ? '0' + minutes : minutes;
        return `${hours}:${minutesStr}${ampm}`;
      } else if (start.date) {
        return 'All Day';
      }
      return '';
    }
  }
}
</script>

<style scoped>
.calendar-widget {
  color: #f5f5f5;
  padding-left: 35px;
}

.day-of-week {
  font-size: 48px;
  font-weight: bold;
}
.divider {
  border: 1px solid #f5f5f5;
  width: 80%;
  margin: 10px 0;
}
.event {
  font-size: 24px;
}
</style>
