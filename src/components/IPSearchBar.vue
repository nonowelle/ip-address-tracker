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
      <p class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <span v-else>{{ ipAddress || 'No data' }}</span>
      </p>
    </div>
    <div class="ip-info-item">
      <h4>Location</h4>
      <p class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <span v-else>{{ location || 'No data' }}</span>
      </p>
    </div>
    <div class="ip-info-item">
      <h4>Timezone</h4>
      <p class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <span v-else>{{ timezone || 'No data' }}</span>
      </p>
    </div>
    <div class="ip-info-item">
      <h4>ISP</h4>
      <p class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <span v-else>{{ isp || 'No data' }}</span>
      </p>
    </div>
  </div>
  <div id="map" class="map"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';

const map = ref(null);
const ipAddress = ref('');
const location = ref('');
const timezone = ref('');
const isp = ref('');
const isLoading = ref(false);
const currentMarker = ref(null);
const isFetching = ref(false);

const initMap = () => {
  if (map.value) return; // Prevent multiple map initializations

  map.value = L.map('map').setView([51.505, -0.09], 13);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap contributors',
  }).addTo(map.value);
};

const updateMap = (lat, lng, locationText) => {
  if (!map.value) return;

  if (currentMarker.value) {
    currentMarker.value.remove();
  }

  map.value.setView([lat, lng], 13);
  currentMarker.value = L.marker([lat, lng])
    .addTo(map.value)
    .bindPopup(`Location: ${locationText}`)
    .openPopup();
};

const fetchUserIP = async () => {
  if (isFetching.value) return;

  isFetching.value = true;
  isLoading.value = true;

  try {
    const response = await fetch(
      'https://geo.ipify.org/api/v2/country,city?apiKey=at_cNIcwBVo9OJNAFYWRa0m6VCRcB0Ue'
    );

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();

    ipAddress.value = data.ip;
    location.value = `${data.location.country}, ${data.location.region}`;
    timezone.value = `UTC ${data.location.timezone}`;
    isp.value = data.isp;

    updateMap(data.location.lat, data.location.lng, data.location.region);
  } catch (error) {
    console.error('Could not fetch the user IP address:', error);

    ipAddress.value = 'Error';
    location.value = 'Error';
    timezone.value = 'Error';
    isp.value = 'Error';
  } finally {
    isLoading.value = false;
    isFetching.value = false;
  }
};

onMounted(() => {
  initMap();
  fetchUserIP();
});

onBeforeUnmount(() => {
  if (currentMarker.value) {
    currentMarker.value.remove();
  }
  if (map.value) {
    map.value.remove();
    map.value = null;
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

.loading-spinner {
  display: inline-block;
  width: 20px;
  height: 20px;
  border: 3px solid rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  border-top-color: #2b2dff;
  animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.result {
  min-height: 24px;
  display: flex;
  align-items: center;
  justify-content: flex-start;
}
</style>
