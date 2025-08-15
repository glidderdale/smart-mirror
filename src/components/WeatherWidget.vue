<template>
  <div class="weather-widget">
  <h2 class="time">{{ time }}</h2>
  <div class="date">{{ date }}</div>
    <h3 class="weather">
  <span v-if="weatherLoaded"><img src="/icons/cloud-icon.png" alt="cloud icon" class="icon-weather" /> {{ temperature }}°F {{ description }}</span>
      <span v-else>Loading weather...</span>
    </h3>
    <h3 class="weather-city">Akron, Ohio</h3>
  </div>
</template>

<script>

import { ref, onMounted, onUnmounted } from 'vue';

export default {
  name: 'WeatherWidget',
  setup() {
    const time = ref(getCurrentTime());

  const date = ref(getCurrentDate());
  const temperature = ref('');
  const description = ref('');
  const weatherLoaded = ref(false);

    let timeIntervalId = null;
    let weatherIntervalId = null;

    function getCurrentTime() {
      const now = new Date();
      let hours = now.getHours();
      const minutes = now.getMinutes();
      const ampm = hours >= 12 ? 'pm' : 'am';
      hours = hours % 12;
      hours = hours ? hours : 12; // the hour '0' should be '12'
      const minutesStr = minutes < 10 ? '0' + minutes : minutes;
      return `${hours}:${minutesStr}${ampm}`;
    }


    function getCurrentDate() {
      const now = new Date();
      const options = { weekday: 'short', month: 'numeric', day: 'numeric' };
      return now.toLocaleDateString(undefined, options);
    }

    function updateTime() {
      time.value = getCurrentTime();
      date.value = getCurrentDate();
    }

    async function fetchWeather() {
      // Only fetch between 5am and 10pm
      const now = new Date();
      const hour = now.getHours();
      if (hour < 5 || hour > 22) {
        return;
      }
      // Replace 'YOUR_API_KEY' with your OpenWeatherMap API key
      const apiKey = 'f22431912b6cf368ce89c2b75f132671';
      const url = `http://api.openweathermap.org/data/2.5/forecast?id=5145476&appid=${apiKey}&units=imperial`;
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error('Weather fetch failed');
        const data = await response.json();
        // For /forecast endpoint, use the first forecast entry
        if (data.list && data.list.length > 0) {
          temperature.value = Math.round(data.list[0].main.temp);
          description.value = data.list[0].weather[0].main;
        } else {
          throw new Error('No forecast data');
        }
        weatherLoaded.value = true;
      } catch (e) {
        weatherLoaded.value = false;
        temperature.value = '';
        description.value = 'Weather unavailable';
      }
    }

    onMounted(() => {
      timeIntervalId = setInterval(updateTime, 30000);
      updateTime();
      fetchWeather();
      weatherIntervalId = setInterval(fetchWeather, 1800000); // 30 minutes
    });

    onUnmounted(() => {
      if (timeIntervalId) clearInterval(timeIntervalId);
      if (weatherIntervalId) clearInterval(weatherIntervalId);
    });


  return { time, date, temperature, description, weatherLoaded };
  }
}
</script>

<style scoped>
.weather-widget {
  color: #f5f5f5;
  padding-left: 35px;
}
.time {
  font-size: 48px;
  font-weight: bold;
}

.date {
  font-size: 20px;
  margin-top: -35px;
  font-weight: bold;
  margin-bottom: 20px;
}
.weather {
  font-size: 24px;
}
.weather-city {
  font-size: 24px;
  margin-top: -20px;
}
.icon-weather {
  display: inline-block;
  vertical-align: middle;
  margin-right: 8px;
  width: 36px;
  height: 36px;
  filter: brightness(0) invert(1);
}
</style>
