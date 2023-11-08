<script setup lang="ts">
    import { GameState } from "../logic";
</script>

<script lang="ts">
    export default {
      data() {
        return {
            audio: new Audio("/sounds/158166-Door-Wood-Bathroom-Exterior_POV-Knock-x2-Concise.mp3"),
            audio2: new Audio("/sounds/201805__fartheststar__poker-chips3.wav"),
            audio3: new Audio("/sounds/201807__fartheststar__poker-chips1.wav"),
            audio4: new Audio("/sounds/289113-Dropping_on_the_table_a_pile_of_small_cards_03.mp3"),
            raised: 100
        }
      },
      props: {
        state: {required: true, type: Object},
        set_state: { type: Function },
        next_player_speaks: { type: Function },
        fold: { type: Function },
        check: { type: Function },
        raise: { type: Function },
      },
      computed: {
        computedRaised() {
            return this.raised
        },
        maxRaiseAmount() {
            return this.state.players[0].money
        }
      },
      methods:{
        handleClickFold() {
            let st = this.fold(this.state)
            st = this.next_player_speaks(st)
            this.set_state(st)
            this.audio4.play()
        },
        betAmount() {
            return this.state.call - this.state.players[0].bet
        },
        handleClickCheck() {
            if (this.state.call - this.state.players[0].bet == 0)
                this.audio.play()
            else
                this.audio2.play()
            let st = this.check(this.state)
            st = this.next_player_speaks(st)
            this.set_state(st)
        },
        handleClickRaise() {
            console.log("raised: "+this.raised)
            let st=this.raise(this.state,+this.raised)
            st=this.next_player_speaks(st)
            this.set_state(st)
            this.audio3.play()
        }
      }

    }
</script>

<template>
    <div class="Panel">
                <div>
                <a class="myButton" @click="handleClickFold">Fold</a>
                </div>
                <div>
                <a class="myButton" @click="handleClickCheck">Check {{ betAmount() }}</a>
                </div>
                <div>
                <a class="myButton" @click="handleClickRaise">Raise {{raised}}</a>
                </div>
                <div>
                    <input type="range" v-model="computedRaised" min="100" :max="maxRaiseAmount"/>
                </div>
           </div>
</template>
  
