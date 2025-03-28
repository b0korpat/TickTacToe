<template>
  <div class="game container-fluid d-flex align-items-center justify-content-center flex-column mt-4">
    <div class="mt-3 mb-3 h3 ">
      Játékos: {{ currentPlayer }}
    </div>
    <div class="board">
      <div v-for="(cell, index) in board" :key="index" class="cell bg-info border" @click="makeMove(index)">
        {{ cell }}
      </div>
    </div>
    <div v-if="winner" class="winner">
      Nyertes: {{ winner }}
    </div>
    <button class="btn btn-primary mt-3" @click="resetGame">Újra</button>

    <div class="log mt-3">
      <h4>Játék előzmény</h4>
      <ul>
        <li v-for="(entry, index) in log" :key="index">
            <p>{{entry}}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'

const board = ref([null, null, null, null, null, null, null, null, null])
const currentPlayer = ref('X')
const winner = ref(null)
const log = ref([])

const makeMove = (index) => {
  if (!board.value[index] && !winner.value) {
    board.value[index] = currentPlayer.value
    if (checkWinner()) {
      winner.value = currentPlayer.value
log.value.unshift(currentPlayer.value+" nyert")
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
.board {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  gap: 5px;
}

.cell {
  width: 100px;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  border: 1px solid #000;
  cursor: pointer;
}

.winner {
  margin-top: 20px;
  font-size: 1.5em;
}
</style>