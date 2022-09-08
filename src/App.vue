<script setup lang="ts">
  import Game from './components/Game.vue'
</script>

<script lang="ts">
export default {
  data() {
    return {
      image: '/images/background.jpg'
    }
  }
}
</script>


<template>
    <div class="App" v-bind:style="{ backgroundImage: 'url(' + image + ')' }">
      <Game />
    </div>
</template>

<style scoped>
</style>
