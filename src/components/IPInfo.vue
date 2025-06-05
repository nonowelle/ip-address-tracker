<template>
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
</template>

<script setup>
defineProps({
  ipAddress: {
    type: String,
    default: '',
  },
  location: {
    type: String,
    default: '',
  },
  timezone: {
    type: String,
    default: '',
  },
  isp: {
    type: String,
    default: '',
  },
  isLoading: {
    type: Boolean,
    default: false,
  },
});
</script>

<style scoped lang="scss">
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

@media (max-width: 768px) {
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
}
</style>
