<script setup lang="ts">
    import Card from './Card.vue'
</script>

<script lang="ts">
    export default {
      data() {
        return {
            
        }
      },
      components: {
        Card
      },
      props: {
        cards: {type: Array<String>}
      }
    }
</script>

<template>
    <div class="Hand">
            <Card v-for="card in cards" :card="card">
            </Card>
    </div>
</template>
  
