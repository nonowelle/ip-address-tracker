<script setup></script>

<template>
  <div class="hero-background">
    <h1>IP Address Tracker</h1>
    <div class="search-bar">
      <input type="text" placeholder="Search for any IP address or domain" />
      <button>></button>
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
  </div>
</template>

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
  border-radius: 0 0 24px 24px;
  position: relative;
  overflow: hidden;
  color: white;
  text-align: center;
  padding: 2rem 1rem;
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
  gap: 24px;
  font-size: 18px;
  font-weight: 100;
  margin-top: 2rem;
  background-color: white;
  border-radius: 24px;
  padding: 30px 24px;
  color: gray;
  width: 90vw;
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
</style>
