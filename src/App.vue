<script setup>
import HeaderTop from './components/HeaderTop.vue'
import GameCanvas from './components/GameCanvas.vue'
import Footer from './components/Footer.vue'
</script>

<template>
  <main>
    <HeaderTop />
    <GameCanvas />
    <Footer />
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  place-items: center;
  gap: 1rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-bottom: 3vh;
  }

  header {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
