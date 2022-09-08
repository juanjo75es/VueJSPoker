<script lang="ts">
    export default {
      data() {
        return {
            image: '/images/back.png'
        }
      },
      props: {
        
      }
    }
</script>

<template>
    <div class="Deck">
        <img class="reverso reversodeck" :src="image"/>
        <img class="reverso reversodeck" :src="image"/>
        <img class="reverso reversodeck" :src="image"/>
        <img class="reverso reversodeck" :src="image"/>
        <img class="reverso reversodeck" :src="image"/>
   </div>
</template>
  
