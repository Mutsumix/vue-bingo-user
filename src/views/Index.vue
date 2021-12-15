<template>
  <div class="app-content">
    <bingo></bingo>
  </div>
</template>

<script>
import Bingo from '../components/Bingo'

export default {
  name: 'home',
  components: {
    Bingo,
  }
}
</script>

<style>
.app-content {
  background-image: linear-gradient(to top right, rgba(162, 209, 34, 0.7), rgba(0, 255, 85, 0.404));
  background-size: cover;
}
</style>
