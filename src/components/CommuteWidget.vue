<template>
  <div class="commute-widget">
  <h2 class="widget-title"><img src="/icons/car-icon.png" alt="car icon" class="icon-car" /> Drive Times</h2>
    <h3 class="route">
      Westfield:
      <span v-if="westfieldDriveTime">{{ westfieldDriveTime }}</span>
      <span v-else>Loading...</span>
    </h3>
    <h3 class="route">Ohm: 
      <span v-if="ohmDriveTime">{{ ohmDriveTime }}</span>
      <span v-else>Loading...</span>
    </h3>
    <h3 class="route">Painting: 
      <span v-if="paintingDriveTime">{{ paintingDriveTime }}</span>
      <span v-else>Loading...</span>
    </h3>  
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "DriveTimeMapbox",
  data() {
    return {
  westfieldDriveTime: null,
  ohmDriveTime: null,
  paintingDriveTime: null,
      mapboxToken: "pk.eyJ1IjoiZ2xpZGRlcmRhbGUiLCJhIjoiY21lZDFoODQ1MDVibzJscTQwZm1iYmEyYSJ9.-rszfZJJy0P9z73_xqICAQ",
      // coordinates in [longitude,latitude] format
      homeCoords: [-81.48940641306969, 41.01285731873302],
      westfieldCoords: [-81.93472232109048, 41.02625480116914],
      ohmCoords: [-81.52372901373897, 41.075239235622306],
      paintingWithATwistCoords: [-81.42903588196202, 40.88086115066244]
    };
  },
  mounted() {
  this.fetchAllDriveTimes();
    this.driveInterval = setInterval(() => {
      const now = new Date();
      const hour = now.getHours();
      // Only fetch between 6am and 10am
      if (hour >= 6 && hour <= 10) {
  this.fetchAllDriveTimes();
      }
    }, 15 * 60 * 1000); // every 15 minutes
  },
  beforeUnmount() {
    if (this.driveInterval) clearInterval(this.driveInterval);
  },
  methods: {
    async fetchAllDriveTimes() {
      await Promise.all([
        this.fetchDriveTime('westfieldDriveTime', this.westfieldCoords),
        this.fetchDriveTime('ohmDriveTime', this.ohmCoords),
        this.fetchDriveTime('paintingDriveTime', this.paintingWithATwistCoords)
      ]);
    },
    async fetchDriveTime(target, destCoords) {
      try {
        const url = `https://api.mapbox.com/directions/v5/mapbox/driving-traffic/${this.homeCoords.join(",")};${destCoords.join(",")}?geometries=geojson&overview=full&access_token=${this.mapboxToken}`;
        const res = await axios.get(url);
        if (res.data.routes && res.data.routes.length > 0) {
          // duration is returned in seconds
          const seconds = res.data.routes[0].duration;
          this[target] = this.formatTime(seconds);
        } else {
          this[target] = "Unable to get drive time";
        }
      } catch (error) {
        console.error(error);
        this[target] = "Error fetching drive time";
      }
    },
    formatTime(seconds) {
      const minutes = Math.round(seconds / 60);
      return `${minutes} mins`;
    }
  }
};
</script>

<style scoped>
.commute-widget {
  color: #f5f5f5;
  padding-right: 35px;
}
.widget-title {
  font-size: 48px;
  font-weight: bold;
}
.divider {
  border: 1px solid #f5f5f5;
  width: 80%;
  margin: 10px 0;
}
.route {
  font-size: 24px;
}
.icon-car {
  display: inline-block;
  vertical-align: middle;
  margin-right: 8px;
  width: 70px;
  height: 70px;
  margin-top: -5px;
  filter: brightness(0) invert(1);
}
</style>
