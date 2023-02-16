<template>
  <h1>Harry Potter Memory</h1>

  <TransitionGroup tag="section" class="game-board" name="shuffle-card">
    <Card v-for="card in cardList" 
    :key="`card-${card.value}-${card.variant}`"
    :value="card.value"
    :matched="card.matched"
    :visible="card.visible"
    :position="card.position"
    @select-card="flipCard"/>
  </TransitionGroup>

  <h2>{{ status }}</h2>

  <button id="restartBtn" @click="restartGame">Restart Game</button>
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

    // const shuffleCards = () => {
    //   cardList.value = _.shuffle(cardList.value)
    // }

    function shuffleCards() {
      cardList.value.sort(() => {
      return 0.5 - Math.random();
     });

     return cardList.value;
    }

    const restartGame = () => {
      // cardList.value = _.shuffle(cardList.value)
      shuffleCards()

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card, 
          matched: false,
          position: index,
          visible: false,
        }
      })
    }
    
    const cardItems = ['luna', 'hermione', 'fleur', 'neville', 'ron', 'severus', 'harry', 'twins']

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
    
    shuffleCards() 

    cardList.value.forEach((card, index) => {
     card.position = index;
    });

    // cardList.value = cardList.value.map((card, index) => {
    //   return {
    //    ...card, 
    //    position: index
    //   }
    // })

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
       if (currentValue.length === 2) {
        const cardOne = currentValue[0]
        const cardTwo = currentValue[1] 

        if (cardOne.faceValue === cardTwo.faceValue){
          
           cardList.value[cardOne.position].matched = true
           cardList.value[cardTwo.position].matched = true 
        } else {
          setTimeout(() => {
            cardList.value[cardOne.position].visible = false
            cardList.value[cardTwo.position].visible = false 
          }, 1000)
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
      shuffleCards, 
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
} 

.status {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 18px;
  text-transform: uppercase;
}

.game-board{
  display: grid; 
  grid-template-columns: repeat(4, 160px); 
  grid-template-rows: repeat(4, 160px);
  gap: 20px;
  justify-content: center;
}

.shuffle-card-move{
   transition: transform 0.8s ease-in; 
}

#restartBtn{
  color:rgb(255, 255, 255);
  border-radius: 5px;
  border-color:#6221d2; 
  background-color: #b082fe;
  font-size: 20px; 
  padding: 8px; 
  transition-duration: 0.4s;
}

#restartBtn:hover{
  color: white; 
  background-color: #6221d2;
  cursor:pointer; 
}
</style>
