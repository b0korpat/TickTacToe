<template>
  <div class="max-w-2xl mx-auto p-6 bg-gradient-to-br from-gray-50 to-white rounded-xl shadow-lg">
    <h1 class="text-3xl font-bold text-center text-gray-800 mb-6">Tic Tac Toe</h1>
    
    <div class="flex justify-center mb-8">
      <div 
        class="px-6 py-3 rounded-full text-center transition-all duration-300"
        :class="currentPlayer === 'X' ? 'bg-blue-100 text-blue-700' : 'bg-pink-100 text-pink-700'"
      >
        <div class="text-sm opacity-75">Következő játékos</div>
        <div class="text-2xl font-bold">{{ currentPlayer }}</div>
      </div>
    </div>
    
    <div class="bg-gray-700 p-2 rounded-lg shadow-md mx-auto max-w-xs">
      <div class="grid grid-cols-3 gap-2">
        <div 
          v-for="(cell, index) in board" 
          :key="index" 
          class="w-24 h-24 flex items-center justify-center bg-white rounded-md text-4xl font-bold transition-all duration-200 shadow-inner" 
          :class="{
            'text-blue-600 hover:bg-blue-50': cell === 'X',
            'text-pink-600 hover:bg-pink-50': cell === 'O',
            'hover:shadow-md hover:scale-[1.02] cursor-pointer': !cell && !winner,
            'cursor-default': cell || winner
          }"
          @click="makeMove(index)"
        >
          <span v-if="cell" class="transform transition-all scale-in">{{ cell }}</span>
        </div>
      </div>
    </div>
    
    <div 
      v-if="winner" 
      class="mt-6 py-3 px-6 bg-gradient-to-r from-gray-50 to-white rounded-lg shadow text-center text-xl animate-fade-in"
    >
      <span 
        :class="winner === 'X' ? 'text-blue-600 font-bold' : 'text-pink-600 font-bold'"
      >{{ winner }}</span> nyert!
    </div>
    
    <div class="flex justify-center mt-6">
      <button 
        @click="resetGame" 
        class="px-6 py-3 bg-gradient-to-r from-blue-500 to-blue-600 text-white font-semibold rounded-full shadow-md hover:shadow-lg hover:-translate-y-0.5 active:translate-y-0 transition-all duration-200"
      >
        Új játék
      </button>
    </div>
    
    <div class="mt-8">
      <h3 class="text-lg font-semibold text-gray-800 pb-2 border-b-2 border-gray-200">Játék előzmény</h3>
      <div class="mt-2 max-h-48 overflow-y-auto bg-gray-50 rounded-lg p-2 shadow-inner">
        <div 
          v-for="(entry, index) in log" 
          :key="index" 
          class="py-2 px-3 mb-2 bg-white rounded border-l-4 border-blue-500 shadow-sm animate-fade-in"
        >
          {{ entry }}
        </div>
        <div v-if="log.length === 0" class="p-4 text-gray-500 italic text-center">
          Nincs még játék előzmény
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const board = ref([null, null, null, null, null, null, null, null, null])
const currentPlayer = ref('X')
const winner = ref(null)
const log = ref([])

const makeMove = (index) => {
  if (!board.value[index] && !winner.value) {
    board.value[index] = currentPlayer.value
    if (checkWinner()) {
      winner.value = currentPlayer.value
      log.value.unshift(currentPlayer.value + " nyert")
    } else if (board.value.every(cell => cell !== null)) {
      log.value.unshift("Döntetlen")
    } else {
      currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
    }
  }
}

const checkWinner = () => {
  const winningCombinations = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8],
    [0, 3, 6], [1, 4, 7], [2, 5, 8],
    [0, 4, 8], [2, 4, 6]
  ]
  return winningCombinations.some(combination => {
    const [a, b, c] = combination
    return board.value[a] && board.value[a] === board.value[b] && board.value[a] === board.value[c]
  })
}

const resetGame = () => {
  board.value = Array(9).fill(null)
  currentPlayer.value = 'X'
  winner.value = null
}
</script>

<style scoped>
.scale-in {
  animation: scaleIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.animate-fade-in {
  animation: fadeIn 0.3s ease-out;
}

@keyframes scaleIn {
  0% {
    transform: scale(0);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>