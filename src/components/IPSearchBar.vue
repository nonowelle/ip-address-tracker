<template>
  <div class="hero-background">
    <h1>IP Address Tracker</h1>
    <div class="search-bar">
      <input type="text" placeholder="Search for any IP address or domain" />
      <button>></button>
    </div>
  </div>
  <div class="ip-info">
    <div class="ip-info-item">
      <h4>IP Address</h4>
      <p class="result">Result</p>
    </div>
    <div class="ip-info-item">
      <h4>Location</h4>
      <p class="result">Result</p>
    </div>
    <div class="ip-info-item">
      <h4>Timezone</h4>
      <p class="result">Result</p>
    </div>
    <div class="ip-info-item">
      <h4>ISP</h4>
      <p class="result">Result</p>
    </div>
  </div>
  <div id="map" class="map"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
const map = ref(null);

const initMap = () => {
  map.value = L.map('map').setView([51.505, -0.09], 13);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors',
  }).addTo(map.value);

  L.marker([51.5, -0.09])
    .addTo(map.value)
    .bindPopup('A sample popup.')
    .openPopup();
};

onMounted(() => {
  initMap();
});

onBeforeUnmount(() => {
  if (map.value) {
    map.value.remove();
  }
});
</script>

<style scoped lang="scss">
h1 {
  color: white;
  font-size: 36px;
  font-weight: 300;
}
.hero-background {
  background: linear-gradient(135deg, #2b2dff 0%, #5757d1 50%, #6c3aff 100%);
  min-height: 300px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  text-align: center;
}
.search-bar {
  display: flex;
  align-items: stretch;
  justify-content: center;
  width: 100%;
  max-width: 500px;
  margin-top: 2rem;
  background-color: white;
  border-radius: 24px;
  font-weight: 100;
}

input {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 90vw;
  padding: 20px 30px;
  font-size: 18px;
  min-height: 60px;
  border-radius: 24px 0 0 24px;
  background-color: white;
  border: none;
  outline: none;
}
button {
  background-color: black;
  color: white;
  border-radius: 0 24px 24px 0;
  padding: 0 24px;
  height: auto;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  cursor: pointer;
}
.ip-info {
  display: flex;
  align-items: center;
  justify-content: center;
  justify-self: center;
  gap: 24px;
  font-size: 18px;
  font-weight: 100;
  margin-top: 2rem;
  background-color: white;
  border-radius: 24px;
  padding: 30px 24px;
  color: gray;
  width: 90%;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 32%;
  left: 50%;
  transform: translateX(-50%);
  z-index: 2;
}
h4 {
  font-size: 14px;
  font-weight: 200;
}
.ip-info-item {
  display: flex;
  flex-direction: column;
  align-items: start;
  justify-content: stretch;
  gap: 18px;
  width: 20%;
  border-right: 1px solid grey;

  &:last-of-type {
    border: none;
  }
  .result {
    color: black;
    font-weight: bold;
    font-size: 24px;
  }
}
.map {
  height: 50vh;
  width: 100%;
  position: relative;
  z-index: 1;
}
</style>
