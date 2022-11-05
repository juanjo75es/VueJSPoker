<script setup lang="ts">
    import Card from './Card.vue'
    import * as logic from '../logic'
    import { defineComponent } from 'vue'    
</script>

<script lang="ts">
    export default defineComponent({
      data() {
        return {
            image: '/images/chip.png',
            back_image: '/images/back.png',
            button_image: '/images/dealer-button.png',
            classname: String
        }
      },
      props: {
        type: {type: String},
        player: {type: Object},
        eliminated: {type: Boolean},
        cards: {type: Array<String>},
        hasButton: {type: Boolean},
        won: {type: Number}
      },
      beforeMount() {
        this.classname="jugador jugador"+(this.player.id+1)
        if(this.eliminated)
            this.classname="eliminated_player jugador jugador"+(this.player.id+1)
        //console.log("setup: "+this.classname)
      }
    })
</script>

<template>
    <div :class="classname" v-if="!eliminated">
                <div v-if="player.playing && player.showCards" class="cartas">
                    <Card v-for="card in cards" :card="card">
                    </Card>
                </div>
                <div v-else-if="player.playing" class="cartas">
                    <img class="reverso" :src="back_image"/>
                    <img class="reverso" :src="back_image"/>
                </div>
                <div class="card">
                    <div class="card_img">
                        <img class="css-border" :src="player.picture" />
                    </div>
                    <div class="card_info">
                        <div>{{player.name}}</div>
                        <div>{{player.money}} </div>
                        <div v-if="won>0" class="winner">{{won}}</div>
                    </div>
                </div>
                <div class="winning-hand">
                    <Card v-if="won>0" v-for="card in player.best_hand" :card="card">
                    </Card>
                </div>
                <div v-if="player.bet>0" class="bet"><img class="chip" :src="image"/> {{player.bet}}</div>
                <img v-if="hasButton" class="dealer-button" :src="button_image"/>
                    
           </div>
</template>
  
