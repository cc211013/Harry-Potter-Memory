<template>
  <h1>Harry Potter Memory</h1>

  <transition-group tag="section" class="game-bord" name="shuffle-card">
    <Card v-for="card in cardList" 
    :key="`card-${card.value}-${card.variant}`"
    :value="card.value"
    :matched="card.matched"
    :visible="card.visible"
    :position="card.position"
    @select-card="flipCard"/>
  </transition-group>

  <h2>{{ status }}</h2>

  <p>Remaining Pairs: {{ remainingPairs }}</p>
  <button @click="restartGame">Restart Game</button>
</template>


<script>
//cd memoryGame 
//npm run dev 

import _ from 'lodash'
import { computed, ref, watch } from 'vue'
import Card from './components/Card.vue'

export default {
  name:'App',
  components: {
    Card
  },
  setup() {
    const cardList = ref([])
    const userSelection = ref([])

    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return 'Player wins!'
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`
      }
    })

    const remainingPairs = computed(() => {
       const remainingCards = cardList.value.filter
       (card => card.matched === false).length

       return remainingCards / 2 
    })

    const shuffleCards = () => {
      cardList.value = _.shuffle(cardList.value)
    }

    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value)

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card, 
          matched: false,
          position: index,
          visible: false,
        }
      })
    }
    
    const cardItems = []

    cardItems.forEach(item => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: false,
        position: null,
        matched: false
      })

      cardList.value.push({
        value: item,
        variant: 2,
        visible: false,
        position: null,
        matched: false
      })
    })

    cardList.value = cardList.value.map((card, item) => {
      return {
       ...card, 
       position: index
      }
    })

    const flipCard = payload => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        
       if (userSelection.value[0].position === payload.position && userSelection.value[0].faceValue === payload.faceValue) {
        return 
       } else {
        userSelection.value[1] = payload
       }
      } else {
        userSelection.value[0] = payload
      }
    }

    watch(userSelection, (currentValue) => {
       if (currentValue.lenght === 2) {
        const cardOne = currentValue[0]
        const cardTwo = currentValue[1] 

        if (cardOne.faceValue === cardTwo.faceValue){
          
           cardList.value[cardOne.position].matched = true
           cardList.value[cardTwo.position].visible = false 
        } else {
          setTimeout(() => {
            cardList.value[cardOne.position].visible = false
            cardList.value[cardTwo.position].visible = false 
          }, 2000)
        }

        userSelection.value.length = 0 
       }
    }, 
    {deep: true})

    return {
      cardList,
      flipCard, 
      userSelection, 
      status, 
      restartGame, 
    }
  }
}

</script>


<style>

html{
  background-color: rgb(206, 212, 239);
}

#app{
  font-family: Arial, Helvetica, sans-serif;
  text-align: center;
  color:#6221d2; 
  /* background-color: rgb(205, 255, 255); */
  height: 100%;
  width: 100%;  
} 

.game-board{
  display: grid; 
  grid-template-columns: 100px 100px 100px 100px; 
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px; 
  grid-row-gap: 30px; 
  justify-content: center;
}

.shuffle-card-move{
   transition: transform 0.8s ease-in; 
}

</style>
