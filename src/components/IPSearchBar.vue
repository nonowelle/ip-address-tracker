<script setup></script>

<template>
  <div class="hero-background">
    <h1>IP Address Tracker</h1>
    <div class="search-bar">
      <input type="text" placeholder="Search for any IP address or domain" />
      <button>></button>
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
</style>
