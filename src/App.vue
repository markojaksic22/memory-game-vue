<template>
  <h1>Memory game</h1>
  <transition-group tag="section" class="game-board" name="shuffle-card">
    <Card v-for="(card) in cardList" 
    :key="`${card.value}-${card.variant}`" 
    :matched="card.matched"
    :value="card.value"
    :visible="card.visible"
    :position="card.position"
    @select-card="flipCard" />
  </transition-group>
  <h2> {{status}} </h2>
  <button v-if="newPlayer" @click="startGame" class="button">  
    Start Game
  </button>
  <button v-else @click="restartGame" >
    Restart Game
  </button>
  </template>

<script>
import _ from 'lodash'
import { computed, ref, watch } from 'vue'
import Card from './components/Card'

export default {
  
  name: 'App',
  components:{
    Card
  },
  setup(){
    const cardList = ref([])
    const newPlayer = ref(true)
    const userSelection = ref([])

    const startGame = () => {
      newPlayer.value = false

      restartGame()
    }

    const status = computed (() =>{
      if(remainingPairs.value === 0){
        return 'You won!'
      }else{
        return `Remaining pairs: ${remainingPairs.value}`
      }
    })
    

    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        card => card.matched === false).length
        
        return remainingCards / 2

    })


    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value)

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false
        }
      })
    }

    const cardItems = ['bat',
      'candy',
      'cauldron',
      'cupcake',
      'ghost',
      'moon',
      'pumpkin',
      'witch-hat']

    cardItems.forEach(item =>{
      cardList.value.push({
        value: item,
        variant:1,
        visible: false,
        position: null,
        matched: false
      })

      cardList.value.push({
        value: item,
        variant:2,
        visible: false,
        position: null,
        matched: false
      })
    })

    cardList.value = cardList.value.map((card, index)=>{
      return {
        ...card,
        position:index
      }
    } )

    const flipCard = payload => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]){
        if (userSelection.value[0].position === payload.position&&
        userSelection.value[0].faceValue===payload.faceValue
        ){
          return
        }else{
        userSelection.value[1] = payload
        }
      } else {
        userSelection.value[0] = payload
      }
    }


    watch(userSelection, (currentValue) =>{
      if(currentValue.length === 2) {
        const cardOne = currentValue[0]
        const CardTwo = currentValue[1]

        if(cardOne.faceValue === CardTwo.faceValue){
          cardList.value[cardOne.position].matched = true
          cardList.value[CardTwo.position].matched = true
        } else{
          setTimeout(() => {
            cardList.value[cardOne.position].visible = false
            cardList.value[CardTwo.position].visible = false
          },1900)  
        }

        
        userSelection.value.length = 0
      }
    },
    {deep: true})

    
    return{
      cardList,
      flipCard,
      userSelection,
      status,
      restartGame,
      startGame,
      newPlayer
    }
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;

}
h1{
  margin-top: 0;
}
#app {
  padding-top: 60px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffff;
  background-image: url('../public/images/page-bg.png');
  background-color: #010311b9;
  height: 100vh;
}


.game-board{
  display: grid;
  grid-template-columns: 120px 120px 120px 120px;
  grid-template-rows: 120px 120px 120px 120px;
  grid-column-gap: 22px;
  grid-row-gap: 22px;
  justify-content: center;
}
.shuffle-card-move{
  transition: transform 0.8s ease-in;
}
button {
  background-color: #4CAF50; /* Green */
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
}

</style>
