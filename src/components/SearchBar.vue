<template>
  <div class="search-bar" :class="{ 'has-error': errorMessage }">
    <input
      type="text"
      v-model="searchInput"
      :placeholder="
        showPlaceholder ? 'Search for any IP address or domain' : ''
      "
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
</template>

<script setup>
import { ref, watch } from 'vue';

const searchInput = ref('');
const errorMessage = ref('');
const showPlaceholder = ref(true);

const emit = defineEmits(['search']);

// Watch for input changes to show placeholder again
watch(searchInput, (newValue) => {
  if (newValue.trim()) {
    showPlaceholder.value = true;
  }
});

const getLocationByIP = async (input) => {
  const apiKey = import.meta.env.VITE_IPIFY_API_KEY;
  let url = `https://geo.ipify.org/api/v2/country,city?apiKey=${apiKey}`;

  if (input.includes('www.')) {
    url += `&domain=${input}`;
  }
  // Check if input is a valid IP address (only numbers and dots)
  else if (/^\d{1,3}(\.\d{1,3}){3}$/.test(input)) {
    url += `&ipAddress=${input}`;
  } else {
    throw new Error(
      'Please enter a valid IP address (e.g., 192.168.1.1) or domain'
    );
  }

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error('Invalid IP address or domain');
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error fetching location data:', error);
    throw error;
  }
};

const handleSearch = async () => {
  if (!searchInput.value.trim()) {
    errorMessage.value = 'Please enter an IP address or domain';
    showPlaceholder.value = false;
    return;
  }

  try {
    const data = await getLocationByIP(searchInput.value);
    emit('search', data);
  } catch (error) {
    errorMessage.value = error.message;
    showPlaceholder.value = false;
  }
};
</script>

<style scoped lang="scss">
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

.error-message {
  color: #ff4444;
  font-size: 14px;
  text-align: left;
  position: absolute;
  left: 16px;
  top: 16px;
}

@media (max-width: 768px) {
  .search-bar {
    position: absolute;
    top: 33%;
    width: 90%;
    max-width: none;
    margin-top: 0;
  }
}

@media (max-width: 375px) {
  .search-bar {
    top: 38%;
    margin-top: 0;
  }
}
</style>
