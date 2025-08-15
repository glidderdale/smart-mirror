
<script setup>
import WeatherWidget from './components/WeatherWidget.vue';
import CalendarWidget from './components/CalendarWidget.vue';
import CommuteWidget from './components/CommuteWidget.vue';
</script>



<template>
  <div class="main-layout">
    <div class="top-left">
      <WeatherWidget />
    </div>
    <div class="bottom-left">
      <CalendarWidget />
    </div>
      <div class="top-right">
      <CommuteWidget />
    </div>
  </div>
</template>

<style scoped>
.main-layout {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
}
.top-left {
  position: absolute;
  top: 0;
  left: 0;
  pointer-events: auto;
}
.bottom-left {
  position: absolute;
  bottom: 0;
  left: 0;
  pointer-events: auto;
}
.top-right {
  position: absolute;
  top: 0;
  right: 0;
  pointer-events: auto;
}
</style>
