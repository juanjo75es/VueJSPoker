
<script setup lang="ts">
    import * as logic from '../logic'
    import * as utils from '../utils'
    import Start from './Start.vue'
    import Victory from './Victory.vue'
    import Gameover from './Gameover.vue'
    import Hand from './Hand.vue'
    import Deck from './Deck.vue'
    import Pot from './Pot.vue'
    import Player from './Player.vue'
    import Panel from './Panel.vue'
</script>
  
<script lang="ts">
    export default {
      data() {
        let l_players=[
              new logic.TPlayer(
                0, 
                'Juanjo', 
                logic.INITIAL_MONEY, 
                0, 
                0, 
                false, 
                true,
                false,
                'https://miro.medium.com/fit/c/176/176/1*kQwIpdh8bFFQXS5876RV5w.jpeg'
              ),
              new logic.TPlayer(1, 'Player 2', logic.INITIAL_MONEY, 0, 0, false, true, false, 'https://i.pravatar.cc/75?kk=1'),
              new logic.TPlayer(2, 'Player 3', logic.INITIAL_MONEY, 0, 0, false, true, false, 'https://i.pravatar.cc/75?kk=2'),
              new logic.TPlayer(3, 'Player 4', logic.INITIAL_MONEY, 0, 0, false, true, false, 'https://i.pravatar.cc/75?kk=3'),
            ]
        return {
          players: l_players,
            state: new logic.GameState(l_players),
            audio1: "/sounds/117482-Casino_ambience_at_game_tables_1.mp3"
        }
      },
      components: {
        Start,
        Victory,
        Gameover,
        Hand,
        Deck,
        Pot,
        Player
      },
      created(){
        console.log("created")

        this.game_logic()

        function interval(){
          let l=400
          if(this.state.bListening)
          {
            if(this.state.speaking_player!=0)
            {
              this.state = logic.next_player_speaks(this.state)
              this.game_logic()
            }
            
          }
          else 
          {
            this.state.players_asked=0
            this.state.stage = (this.state.stage+1)%logic.NSTATES
            
            if(this.state.stage==8)
            {
              l=5000
            }
            console.log("useEffect "+this.state.stage)
            this.game_logic()
          }
          mytimeout=setTimeout(interval.bind(this),l)
        }
        let mytimeout=setTimeout(interval.bind(this),400)

      },
      methods:{
        game_logic(){
          switch(this.state.stage)
          {
            case 0://dealing cards      
              /*console.log("test1: "+JSON.stringify(utils.highest_card(
                ["H1","H4","H5","H7","H8"],[],["H4","H5","H8","HB","HD"],[]))
              )*/
              
              for(let i=0;i<logic.NPLAYERS;i++){
                this.state.players[i].bet=0
                if(!this.state.players[i].eliminated)
                {
                  this.state.players[i].playing=true
                  let e1: string | undefined= this.state.available_cards.pop()
                  let e2: string | undefined = this.state.available_cards.pop()
                  this.state.player_cards[i]=[e1 as string,e2 as string]
                }
              }
              this.state.call=logic.INITIAL_BET
              this.state.players[0].showCards=true

              this.state=logic.force_bet(this.state, (this.state.button+2)%logic.NPLAYERS, logic.INITIAL_BET)
              this.state=logic.force_bet(this.state, (this.state.button+3)%logic.NPLAYERS, logic.INITIAL_BET/2)
              this.state.speaking_player=(this.state.button+1)%logic.NPLAYERS
              this.state.players_asked=0              
              break;
            case 1://listening players
              this.state=logic.listen_players(this.state)
              break;
            case 2://showing 3 cards
              this.state.hand_cards=[
                this.state.available_cards.pop() as string,
                this.state.available_cards.pop() as string,
                this.state.available_cards.pop() as string
              ]
              break;
            case 3://listening players
                this.state=logic.listen_players(this.state)
              break;
            case 4://showing 1 card
              this.state.hand_cards=[...this.state.hand_cards,
                this.state.available_cards.pop() as string,
              ]
              break;
            case 5://listening players
              this.state=logic.listen_players(this.state)
              break;
            case 6://showing 1 card
              this.state.hand_cards=[...this.state.hand_cards,
                this.state.available_cards.pop() as string,
              ]
              break;
            case 7:
              this.state=logic.listen_players(this.state)
              break;
            case 8: //show cards
              this.state.players[1].showCards=true
              this.state.players[2].showCards=true
              this.state.players[3].showCards=true
              //console.log("table hand: "+JSON.stringify(this.state.hand_cards))
              this.state=logic.reward_winners(this.state)
              break;
            case 9://rewarding winners
              {
                this.state.players[1].showCards=false
                this.state.players[2].showCards=false
                this.state.players[3].showCards=false
                //this.state.players[0].money+=this.state.pot;
                this.state.players[0].bet=0;
                this.state.players[1].bet=0;
                this.state.players[2].bet=0;
                this.state.players[3].bet=0;
                this.state.players[0].won=0;
                this.state.players[1].won=0;
                this.state.players[2].won=0;
                this.state.players[3].won=0;
                
                this.state.players[1].best_hand=[]
                this.state.players[2].best_hand=[]
                this.state.players[3].best_hand=[]
                let neliminated=0
                for(let i=0;i<logic.NPLAYERS;i++)
                {
                  if(this.state.players[i].money<0)
                  {
                    this.state.players[i].eliminated=true
                    neliminated++
                    if(i==0){
                      this.state=logic.game_over(this.state)
                    }
                  }
                  if(neliminated==logic.NPLAYERS-1 && !this.state.players[0].eliminated){
                    this.state=logic.victory(this.state)
                  }
                  if(!this.state.players[i].eliminated){
                  }
                }

                this.state.total_pot=0
                
                this.state.call=logic.INITIAL_BET;
                this.state.button=(this.state.button+1)%logic.NPLAYERS
                this.state.available_cards=logic.shuffle_cards()
                this.state.hand_cards=[]
                this.state.players[0].showCards=false
              }
              break;
          }

        },
        handleclick(){
            const audio = new Audio(this.audio1)
            audio.loop=true
            audio.volume=0.1
            audio.play()
            this.state.screen="playing"
        },
        set_state(st: logic.GameState){
          this.state={...st}
          this.game_logic()
          console.log("set_state")
          return this.state
        }
      },
      watch: {
        state: {
          handler(new_state, old_state){
          },
          deep: false
        },
      }
    }
</script>

<template>
    <div class="Board">
        <img class='table' src='/images/table.png'/>
        <div style="display:none">{{state.stage}}</div>
        <Start v-if="state.screen=='start'" @myonclick="() => {handleclick()}"/>
        <Victory v-if="state.screen=='victory'"/>
        <Gameover v-if="state.screen=='gameover'"/>
        <Hand :cards="state.hand_cards"/>
        <Deck/>
        <Pot :amount="state.total_pot"/>
        <Player key="0" :eliminated="state.players[0].eliminated" :won="state.players[0].won" :hasButton="state.button==0" :type="'human'"  :player="players[0]" :cards="state.player_cards[0]"/>
        <Player key="1" :eliminated="state.players[1].eliminated" :won="state.players[1].won" :hasButton="state.button==1" :type="'computer'"  :player="players[1]" :cards="state.player_cards[1]"/>
        <Player key="2" :eliminated="state.players[2].eliminated" :won="state.players[2].won" :hasButton="state.button==2" :type="'computer'"  :player="players[2]" :cards="state.player_cards[2]"/>
        <Player key="3" :eliminated="state.players[3].eliminated" :won="state.players[3].won" :hasButton="state.button==3" :type="'computer'"  :player="players[3]" :cards="state.player_cards[3]"/>
        <Panel  v-if="state.speaking_player==0 && state.bListening"
          :state="state"
          :fold="logic.fold"
          :check="logic.check"
          :raise="logic.raise"
          :set_state="set_state"
          :next_player_speaks="logic.next_player_speaks"
        />
        <img class='dealer' src='/images/dealer1.jpg'/>
    </div>
</template>
  
