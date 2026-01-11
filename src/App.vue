<template>
  <span>pidro-calc</span>
  <div><span>{{ wins.we }}</span> - <span>{{ wins.they }}</span></div>
  <div v-if="!gameStarted"><button @click="startGame">starta spel</button></div>
  <div v-if="gameStarted">
  <div v-if="dealInProgress">
    <div v-if="!dealTeam">
      <div><span>Vem bjöd?</span></div>
      <button @click="clickTeam('we')">vi</button>
      <button @click="clickTeam('they')">de</button>
    </div>
    <div v-else-if="!dealBid">
      <div><span>Vad bjöds?</span></div>
      <button v-for="i in 9" :key=i @click="clickBid(i+5)">{{ i+5 }}</button>
    </div>
    <div v-else>
      <div><span>Hur många poäng spelades hem?</span></div>
      <button v-for="i in 15" :key=i @click="clickScore(i-1)">{{ i-1 }}</button>
    </div>
  </div>
  <div v-else>
    <button @click="stopGame">avsluta spel</button>
    <button @click="newDeal">ny runda</button>
    <button v-if="deleteLastDeal" @click="deleteDeal">stryk runda</button>
  </div>
  </div>
  <div v-if="games.length">
  <div v-for="game in games" :key="game.id">
    <table>
    <tr><th>vi</th><th>de</th></tr>
    <tr v-for="deal in game.deals" :key="deal.we"><td>{{ deal.we }}</td><td>{{ deal.they }}</td></tr>
    </table>
  </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'App',
  components: {
  },
  setup() {
    const wins = ref({"we": 0, "they":0})
    let currentGame = 0
    const games = ref([])
    const gameStarted = ref(null)
    const startGame = () => {
      currentGame++
      games.value.push({"id":currentGame, "we":0, "they":0, "deals":[]})
      gameStarted.value = 1
    }
    const stopGame = () => {
      gameStarted.value = null
      deleteLastDeal.value = null
    }
    const dealInProgress = ref(null)
    const newDeal = () => {
      dealInProgress.value = 1
    }
    const deleteLastDeal = ref(null)
    const deleteDeal = () => {
      let index = currentGame-1
      games.value[index].deals.pop()
      if (games.value[index].deals.length > 0) {
        games.value[index].we = games.value[index].deals.at(-1).we
        games.value[index].they = games.value[index].deals.at(-1).they
      } else {
        games.value[index].we = 0
        games.value[index].they = 0
      }
      deleteLastDeal.value = null
    }
    const dealTeam = ref(null)
    const clickTeam = (team) => {
      dealTeam.value = team
    }
    const dealBid = ref(null)
    const clickBid = (bid) => {
      dealBid.value = bid
    }
    const clickScore = (score) => {
      let index = currentGame-1
      let we = 0
      let they = 0
      let reminder = 14 - score
      if (score >= dealBid.value) {
        if (dealTeam.value == "we") {
          we = games.value[index].we + score
          they = games.value[index].they + reminder
        } else {
          they = games.value[index].they + score
          we = games.value[index].we + reminder
        }
      } else {
        if (dealTeam.value == "we") {
          we = games.value[index].we - dealBid.value
          they = games.value[index].they + reminder
        } else {
          they = games.value[index].they - dealBid.value
          we = games.value[index].we + reminder
        }
      }
      games.value[index].we = we
      games.value[index].they = they
      games.value[index].deals.push({"we":we, "they":they, "team":dealTeam, "bid":dealBid, "score":score})
      deleteLastDeal.value = 1
      if (we >= 62) {
        wins.value.we++
        stopGame()
      }
      if (they >= 62) {
        wins.value.they++
        stopGame()
      }
      dealInProgress.value = null
      dealTeam.value = null
      dealBid.value = null
    }

    return { wins, games, gameStarted, startGame, stopGame, dealInProgress, newDeal, deleteLastDeal, deleteDeal, dealTeam, clickTeam, dealBid, clickBid, clickScore}
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

table {
    margin-left: auto;
    margin-right: auto;
}
</style>
