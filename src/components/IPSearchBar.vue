<template>
  <div class="hero-background">
    <h1>IP Address Tracker</h1>
    <div class="search-bar" :class="{ 'has-error': errorMessage }">
      <input
        type="text"
        v-model="searchInput"
        placeholder="Search for any IP address or domain"
        @keyup.enter="handleSearch"
        :class="{ error: errorMessage }"
        @click="errorMessage = ''"
      />
      <button @click="handleSearch">
        <img src="@/assets/images/icon-arrow.svg" alt="Search icon" />
      </button>
      <span
        v-if="errorMessage"
        class="error-message"
        @click="errorMessage = ''"
        >{{ errorMessage }}</span
      >
    </div>
  </div>
  <div class="ip-info">
    <div class="ip-info-item">
      <h4>IP Address</h4>
      <div class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <p v-else>{{ ipAddress || 'No data' }}</p>
      </div>
    </div>
    <div class="ip-info-item">
      <h4>Location</h4>
      <div class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <p class="result-text" v-else>{{ location || 'No data' }}</p>
      </div>
    </div>
    <div class="ip-info-item">
      <h4>Timezone</h4>
      <div class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <p v-else>{{ timezone || 'No data' }}</p>
      </div>
    </div>
    <div class="ip-info-item">
      <h4>ISP</h4>
      <div class="result">
        <span v-if="isLoading" class="loading-spinner"></span>
        <p v-else>{{ isp || 'No data' }}</p>
      </div>
    </div>
  </div>
  <div id="map" class="map"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref, watch } from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import '@fortawesome/fontawesome-free/css/all.css';
import iconLocationUrl from '@/assets/images/icon-location.svg';

const map = ref(null);
const ipAddress = ref('');
const location = ref('');
const timezone = ref('');
const isp = ref('');
const isLoading = ref(false);
const currentMarker = ref(null);
const isFetching = ref(false);
const searchInput = ref('');
const isMobile = ref(window.innerWidth <= 768);
const errorMessage = ref('');

const handleResize = () => {
  isMobile.value = window.innerWidth <= 768;
};

const initMap = () => {
  if (map.value) return;

  map.value = L.map('map').setView([51.505, -0.09], 13);
  L.tileLayer(
    'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png',
    {
      attribution: '©OpenStreetMap, ©CartoDB',
      maxZoom: 20,
    }
  ).addTo(map.value);
};

const updateMap = (lat, lng, locationText) => {
  if (!map.value) return;

  if (currentMarker.value) {
    currentMarker.value.remove();
  }

  const locationIcon = L.divIcon({
    html: `<img src="${iconLocationUrl}" alt="marker" />`,
    iconSize: [30, 40],
    className: 'custom-div-icon',
    iconAnchor: [20, 40],
  });

  map.value.setView([lat, lng], 13, { animate: true });
  currentMarker.value = L.marker([lat, lng], { icon: locationIcon }).addTo(
    map.value
  );

  if (isMobile.value) {
    map.value.panBy([0, window.innerHeight * 0.2]);
  }
};

const fetchUserIP = async () => {
  if (isFetching.value) return;

  isFetching.value = true;
  isLoading.value = true;

  try {
    const response = await fetch(
      `https://geo.ipify.org/api/v2/country,city?apiKey=${
        import.meta.env.VITE_IPIFY_API_KEY
      }`
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

const getLocationByIP = async (input) => {
  const apiKey = import.meta.env.VITE_IPIFY_API_KEY;
  let url = `https://geo.ipify.org/api/v2/country,city?apiKey=${apiKey}`;

  // Reset error message
  errorMessage.value = '';

  // Check if input is a domain (contains www)
  if (input.includes('www.')) {
    url += `&domain=${input}`;
  }
  // Check if input is a valid IP address (only numbers and dots)
  else if (/^\d{1,3}(\.\d{1,3}){3}$/.test(input)) {
    url += `&ipAddress=${input}`;
  }
  // Invalid input
  else {
    errorMessage.value =
      'Please enter a valid IP address (e.g., 192.168.1.1) or domain (e.g., www.example.com)';
    return null;
  }

  try {
    const response = await fetch(url);
    if (!response.ok) {
      errorMessage.value = 'Invalid IP address or domain';
      return null;
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error fetching location data:', error);
    errorMessage.value = 'Failed to fetch data';
    return null;
  }
};

const handleSearch = async () => {
  if (!searchInput.value.trim()) {
    errorMessage.value = 'Please enter an IP address or domain';
    return;
  }

  isLoading.value = true;
  try {
    const data = await getLocationByIP(searchInput.value);
    if (data) {
      ipAddress.value = data.ip;
      location.value = `${data.location.country}, ${data.location.region}`;
      timezone.value = `UTC ${data.location.timezone}`;
      isp.value = data.isp;
      updateMap(data.location.lat, data.location.lng, data.location.region);
    }
  } catch (error) {
    console.error('Error searching IP:', error);
  } finally {
    isLoading.value = false;
  }
};

onMounted(() => {
  initMap();
  window.addEventListener('resize', handleResize);

  fetchUserIP();
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', handleResize);
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
@use '@/assets/main.scss';
.hero-background {
  position: relative;
  overflow: hidden;
  background: linear-gradient(120deg, #2b2dff 0%, #5757d1 60%, #6c3aff 100%);
  min-height: 300px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: var(--white);
  text-align: center;
}

.hero-background {
  &::before {
    content: '';
    position: absolute;
    inset: 0;
    background: url('@/assets/images/pattern-bg-desktop.png') center center /
      cover no-repeat;
    opacity: 0.58;
    z-index: 0;
    pointer-events: none;
  }
}

.hero-background > * {
  position: relative;
  z-index: 1;
}

.search-bar {
  display: flex;
  align-items: stretch;
  justify-content: center;
  width: 100%;
  max-width: 500px;
  margin-top: 32px;
  background-color: var(--white);
  border-radius: 24px;
  font-weight: 100;
  position: relative;

  &.has-error {
    input {
      color: white;
    }
  }
}

input {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 90vw;
  min-height: 60px;
  padding: 20px 30px;
  font-size: 18px;
  border-radius: 24px 0 0 24px;
  background-color: var(--white);
  border: none;
  outline: none;
  color: var(--very-dark-gray);

  &.error {
    border: 2px solid #ff4444;
  }

  &::placeholder {
    color: var(--dark-gray);
    opacity: 1;
  }
  &:active {
    color: var(--very-dark-gray);
  }
}

button {
  background-color: var(--black);
  color: var(--white);
  border-radius: 0 24px 24px 0;
  padding: 0 24px;
  height: auto;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  cursor: pointer;

  &:hover {
    background: var(--very-dark-gray);
  }

  &:active {
    background: rgb(22, 21, 21);
  }
}

.ip-info {
  display: flex;
  align-items: stretch;
  min-height: 140px;
  justify-content: center;
  gap: 24px;
  font-size: 18px;
  font-weight: 100;
  margin-top: 2rem;
  background-color: var(--white);
  border-radius: 24px;
  padding: 30px 40px;
  color: gray;
  width: 90%;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 32%;
  left: 50%;
  transform: translateX(-50%);
  z-index: 2;
}

.ip-info-item {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
  flex: 1 1 0;
  border-right: 1px solid grey;
  gap: 12px;
  max-width: 25%;

  &:last-of-type {
    border: none;
  }
  .result {
    color: var(--black);
    font-weight: bold;
    font-size: 22px;
    padding-right: 46px;
  }
}

.map {
  height: 70vh;
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

:deep(.custom-div-icon) {
  img {
    display: block;
    width: 100%;
    height: 100%;
  }
}

.hero-map-lines {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  pointer-events: none;
}

.error-message {
  color: #ff4444;
  font-size: 14px;

  text-align: left;
  position: absolute;

  left: 16px;
  top: 16px;
}

@media (max-width: 768px) {
  .hero-background {
    padding: 32px;
    min-height: 50vh;
    justify-content: flex-start;
  }

  .search-bar {
    position: absolute;
    top: 33%;
    width: 90%;
    max-width: none;
    margin-top: 0;
  }

  .ip-info {
    flex-direction: column;
    gap: 12px;
    padding: 18px 8px;
    top: 0;
    transform: translate(-50%, 67%);
    margin-top: 0;
  }

  .ip-info-item {
    width: 100%;
    border-right: none;
    border-bottom: 1px solid grey;
    padding: 5px 20px;
    justify-content: center;
    align-items: center;
    max-width: none;
    border: none;

    .result {
      padding-right: 0;
    }
  }
  .leaflet-control-container {
    display: none;
  }
}

@media (max-width: 375px) {
  .search-bar {
    top: 38%;
    margin-top: 0;
  }
}
</style>
