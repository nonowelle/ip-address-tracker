<template>
  <div class="hero-background">
    <h1>IP Address Tracker</h1>
    <SearchBar @search="handleSearch" />
  </div>
  <IPInfo
    :ip-address="ipAddress"
    :location="location"
    :timezone="timezone"
    :isp="isp"
    :is-loading="isLoading"
  />
  <div id="map" class="map"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue';
import IPInfo from './IPInfo.vue';
import SearchBar from './SearchBar.vue';
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
const isMobile = ref(window.innerWidth <= 768);

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

const handleSearch = async (data) => {
  isLoading.value = true;
  try {
    ipAddress.value = data.ip;
    location.value = `${data.location.country}, ${data.location.region}`;
    timezone.value = `UTC ${data.location.timezone}`;
    isp.value = data.isp;
    updateMap(data.location.lat, data.location.lng, data.location.region);
  } catch (error) {
    console.error('Error updating IP info:', error);
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

.map {
  height: 70vh;
  width: 100%;
  position: relative;
  z-index: 1;
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

@media (max-width: 768px) {
  .hero-background {
    padding: 32px;
    min-height: 50vh;
    justify-content: flex-start;
    gap: 40px;
  }
  .leaflet-control-container {
    display: none;
  }
}
</style>
