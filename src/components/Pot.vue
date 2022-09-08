<script lang="ts">
    export default {
      data() {
        return {
            image: '/images/chip.png'
        }
      },
      props: {
        amount: Number
      }
    }
</script>

<template>
    <div className="Pot">
        Pot: <img class="chip" :src="image"/>{{amount}}
    </div>
</template>
  
