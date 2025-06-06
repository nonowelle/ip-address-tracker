<template>
  <div class="map" ref="mapDiv"></div>
</template>

<script setup>
import {
  ref,
  onMounted,
  onBeforeUnmount,
  watch,
  nextTick,
  useTemplateRef,
} from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import iconLocationUrl from '@/assets/images/icon-location.svg';

const props = defineProps({
  lat: {
    type: Number,
    required: true,
  },
  lng: {
    type: Number,
    required: true,
  },
  locationText: {
    type: String,
    required: true,
  },
  isLoading: {
    type: Boolean,
    default: false,
  },
});

const mapDiv = useTemplateRef('mapDiv');
const currentMarker = ref(null);
const isMobile = ref(window.innerWidth <= 768);

const handleResize = () => {
  isMobile.value = window.innerWidth <= 768;
};

const updateMap = async (lat, lng, locationText) => {
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

  map.value = L.map(mapDiv).setView([lat, lng], 13, { animate: true });
  L.tileLayer(
    'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png',
    {
      attribution: '©OpenStreetMap, ©CartoDB',
      maxZoom: 20,
    }
  ).addTo(map.value);

  currentMarker.value = L.marker([lat, lng], { icon: locationIcon }).addTo(
    map.value
  );

  if (isMobile.value) {
    map.value.panBy([0, window.innerHeight * 0.2]);
  }
};

watch(
  () => [props.lat, props.lng, props.locationText],
  async ([newLat, newLng, newLocationText]) => {
    if (newLat && newLng) {
      await updateMap(newLat, newLng, newLocationText);
    }
  },
  { immediate: true }
);

onMounted(async () => {
  window.addEventListener('resize', handleResize);
  if (props.lat && props.lng) {
    await updateMap(props.lat, props.lng, props.locationText);
  }
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

@media (max-width: 768px) {
  .leaflet-control-container {
    display: none;
  }
}
</style>
