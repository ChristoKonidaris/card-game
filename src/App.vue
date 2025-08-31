<template>
  <div class="max-w-7xl mx-auto font-sans relative">

    <div v-if="!gameState.started"
      class="flex items-center justify-center min-h-screen bg-gradient-to-br from-black via-gray-900 to-red-900 px-4">
      <div
        class="text-center bg-white/100 backdrop-blur-md rounded-2xl shadow-xl p-10 max-w-3xl border border-yellow-400/50">
        <h1 class="text-5xl font-extrabold text-gray-900 mb-6 tracking-tight text-center">
          <div class="mb-2">
            <span>Welcome to</span>
          </div>
          <div>
            <span class="ml-2">♠️♥️</span>
            <span class="text-yellow-500">Suit Showdown</span>
            <span class="ml-2">♦️♣️</span>
          </div>
        </h1>
        <p class="text-gray-700 mb-8 text-lg">
          A fast and fun multiplayer card game of luck!
        </p>
        <ul class="text-left max-w-xl mx-auto mb-4 list-disc list-outside text-gray-600">
          <li>6 players are dealt 5 cards from two 52-card decks.</li>
          <li>Each players score is the sum of their card values: number cards keep their face value, while J = 11, Q =
            12, K = 13, and A = 11.</li>
          <li>In the event of a tie, each tied players score is recalculated by multiplying the suit values of their
            cards where Diamonds = 1, Hearts = 2, Spades = 3, Clubs = 4.</li>
          <li>The winner is the player with the highest score, or in the case of a tie, suit score.</li>
          <li>In the case of a tied suit score, the game will end in a draw.</li>
        </ul>
        <button @click="((gameState.started = true), dealNewGame())"
          class="inline-flex items-center gap-2 bg-gradient-to-r from-yellow-500 to-yellow-600 hover:from-yellow-600 hover:to-yellow-700 text-black font-bold py-3 px-10 rounded-xl shadow-lg hover:shadow-2xl transform hover:scale-105 transition-all duration-200">
          Start Game
        </button>
      </div>
    </div>

    <div v-else
      class="relative min-h-screen bg-gradient-to-br from-black via-gray-900 to-red-900 px-6 py-10 text-white">
      <button @click="gameState.started = false"
        class="absolute top-5 left-5 bg-gradient-to-r from-yellow-500 to-yellow-600 hover:from-yellow-600 hover:to-yellow-700 text-black font-semibold py-2 px-4 rounded-lg shadow-md hover:shadow-lg transition flex items-center gap-2 z-50">
        <ChevronLeft class="w-4 h-4" />Back
      </button>

      <button v-if="showDebug" @click="loadSimulatedTie"
        class="absolute top-5 right-5 bg-red-600 text-white px-4 py-2 rounded-lg shadow-md hover:shadow-lg transition z-50">
        Force Tie
      </button>

      <div class="text-center mb-8">
        <h1 class="text-4xl font-extrabold text-yellow-500 mb-2 tracking-wide">
          <div>
            <span class="ml-2">♠️♥️</span>
            <span class="text-yellow-500">Suit Showdown</span>
            <span class="ml-2">♦️♣️</span>
          </div>
        </h1>
        <p class="text-gray-300 mb-6">6 Players • 5 Cards Each</p>
        <button @click="dealNewGame"
          class="bg-gradient-to-r from-yellow-500 to-yellow-600 hover:from-yellow-600 hover:to-yellow-700 text-black font-bold py-2 px-6 rounded-lg shadow-lg hover:shadow-xl transform hover:scale-105 transition">
          Deal New Hand
        </button>
      </div>

      <div v-if="gameState.winners.length > 0"
        class="bg-black/50 border-2 border-yellow-500 rounded-xl p-6 text-center mb-8 shadow-lg">
        <div v-if="gameState.winners.length === 1">
          <h2 class="text-2xl font-bold text-yellow-400 mb-2">
            🏆 Winner: Player {{ gameState.winners[0] }}
          </h2>
          <p class="text-gray-200">Score: {{ getPlayerScore(gameState.winners[0]) }}</p>
          <p v-if="getPlayerSuitScore(gameState.winners[0])" class="text-gray-200">Suit Score: {{
            getPlayerSuitScore(gameState.winners[0]) }}</p>
        </div>
        <div v-else>
          <h2 class="text-2xl font-bold text-yellow-400 mb-2">
            🤝 Tie: {{gameState.winners.map(id => `Player ${id}`).join(' and ')}}
          </h2>
          <p class="text-gray-200">Score: {{ getPlayerScore(gameState.winners[0]) }}</p>
          <p v-if="getPlayerSuitScore(gameState.winners[0])" class="text-gray-200">Suit Score: {{
            getPlayerSuitScore(gameState.winners[0]) }}</p>
        </div>
      </div>

      <div class="grid gap-6 grid-cols-1 sm:grid-cols-2 md:grid-cols-3">
        <div v-for="player in gameState.players" :key="player.id" :class="[
          'border-2 rounded-xl p-5 bg-black/60 backdrop-blur-md shadow-lg',
          gameState.winners.includes(player.id)
            ? 'border-yellow-500'
            : 'border-gray-700'
        ]">

          <div class="flex justify-between items-center mb-4">
            <h3 class="text-lg font-bold text-white">Player {{ player.id }}</h3>
            <span v-if="gameState.winners.includes(player.id)" class="text-yellow-400 text-xl">👑</span>
          </div>

          <div class="flex flex-wrap gap-2 justify-center mb-4">
            <div v-for="(card, index) in player.cards" :key="`${card.suit}-${card.value}-${card.deckNumber}-${index}`"
              class="w-12 h-16 border rounded-md flex items-center justify-center font-semibold text-sm bg-white shadow-md"
              :class="{
                'text-red-600': card.suit === 'hearts' || card.suit === 'diamonds',
                'text-gray-900': card.suit === 'spades' || card.suit === 'clubs',
              }">
              {{ formatCard(card) }}
            </div>
          </div>

          <div class="flex flex-col gap-2 text-sm">
            <div class="bg-gray-800 rounded px-3 py-1 text-gray-200">Score: {{ player.score }}</div>
            <div v-if="player.suitScore" class="bg-gray-800 rounded px-3 py-1 text-gray-200">
              Suit Score: {{ player.suitScore }}
            </div>
          </div>
        </div>
      </div>

      <div v-if="errorMessage" class="bg-red-900/80 border-2 border-red-600 text-red-200 rounded-lg p-4 mt-8 shadow-lg">
        <strong>Error:</strong> {{ errorMessage }}
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, reactive, onMounted, onBeforeUnmount } from 'vue'
import { ChevronLeft } from "lucide-vue-next";
import simulatedTie from '../public/simulateTie.json'

// Game state used in UI and logic
const gameState = reactive({
  gameDeck: [],
  players: [],
  winners: [],
  highestScore: 0,
  started: ref(false)
})

const errorMessage = ref('')
const showDebug = ref(false)

const suits = ['hearts', 'diamonds', 'clubs', 'spades']
const values = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'A', 'J', 'Q', 'K']

// Press shift + D to show the force tie button 
// Odds are low there will be an actual tie, added this to simulate the use case
onMounted(() => {
  const handler = (e) => {
    if (e.shiftKey && e.key === 'D') {
      showDebug.value = !showDebug.value
    }
  }
  window.addEventListener('keydown', handler)
  onBeforeUnmount(() => window.removeEventListener('keydown', handler))
})

// Force a tie
function loadSimulatedTie() {
  Object.assign(gameState, simulatedTie)
}

// Format card for display
function formatCard(card) {
  const suitSymbols = {
    hearts: '♥️',
    diamonds: '♦️',
    clubs: '♣️',
    spades: '♠️',
  }

  return `${card.value}${suitSymbols[card.suit]}`
}

// Get player score by ID
function getPlayerScore(playerId) {
  const player = gameState.players.find((p) => p.id === playerId)
  return player ? player.score : 0
}

// Get player suit score by ID
function getPlayerSuitScore(playerId) {
  const player = gameState.players.find((p) => p.id === playerId)
  return player ? player.suitScore : 0
}

function dealNewGame() {
  try {
    errorMessage.value = ''

    // Clear previous game
    gameState.gameDeck = []
    gameState.players = []
    gameState.winners = []
    gameState.highestScore = 0

    // Create the combined deck
    for (let deckNum = 0; deckNum < 2; deckNum++) {
      for (const suit of suits) {
        for (const value of values) {
          gameState.gameDeck.push({ suit, value, deckNumber: deckNum + 1 })
        }
      }
    }

    // Shuffle the combined deck using the Fisher-Yates algorithm
    for (let i = gameState.gameDeck.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1))
        ;[gameState.gameDeck[i], gameState.gameDeck[j]] = [
          gameState.gameDeck[j],
          gameState.gameDeck[i],
        ]
    }

    // Deal 5 cards to each of the 6 players and calculate their scores
    for (let i = 0; i < 6; i++) {
      const playerCards = gameState.gameDeck.splice(0, 5)
      const score = playerCards.reduce((sum, card) => {
        if (card.value === 'A') return sum + 11
        if (card.value === 'J') return sum + 11
        if (card.value === 'Q') return sum + 12
        if (card.value === 'K') return sum + 13

        return sum + parseInt(card.value)
      }, 0)

      gameState.players.push({
        id: i + 1,
        cards: playerCards,
        score: score,
        suitScore: null,
      })

      // Check and set highest score
      if (score > gameState.highestScore) {
        gameState.highestScore = score
      }
    }

    // Filter out any tied high scores
    const checkForTie = gameState.players.filter(
      (player) => player.score === gameState.highestScore,
    )

    if (checkForTie.length === 1) {
      gameState.winners = [checkForTie[0].id]
    } else {
      // Calculate the suit scores for any tied players
      checkForTie.forEach((player) => {
        player.suitScore = player.cards.reduce((total, card) => {
          if (card.suit === suits[0]) return total * 1
          if (card.suit === suits[1]) return total * 2
          if (card.suit === suits[2]) return total * 3
          if (card.suit === suits[3]) return total * 4

          return total
        }, 1)
      })

      const highestSuitScore = Math.max(...gameState.players.map((player) => player.suitScore || 0))
      gameState.winners = checkForTie
        .filter((player) => player.suitScore === highestSuitScore)
        .map((player) => player.id)
    }
  } catch (error) {
    errorMessage.value = `Game error: ${error.message}`
    console.error('Game dealing error:', error)
  }
}
</script>
