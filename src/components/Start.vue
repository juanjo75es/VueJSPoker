<script lang="ts">
    export default {
      data() {
        return {
            image: '/images/background.jpg'
        }
      },
      props: {
        show: Boolean,        
      },
      methods:{
        myclick() {
          // emit with argument
          this.$emit('myonclick')
        }
      }
    }
</script>

<template>
    <div class="overlayDiv"
        v-bind:style="{ backgroundImage: 'url(' + image + ')' }"
    >
        <div class="centered">
            <div>Press to start</div>
            <div>
                <a class="myButton" @click="myclick()">Start</a>
            </div>
        </div>
    </div>
</template>
  
