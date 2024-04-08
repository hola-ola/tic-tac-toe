<script setup lang="ts">
import { ref, reactive } from 'vue'

const winner = ref<string | null>(null)
const isTie = ref<boolean>(false)
const currentPlayer = ref<string>('X')
const gameOver = ref<boolean>(false)

let board = reactive([
  ['', '', ''],
  ['', '', ''],
  ['', '', '']
])

const makeMove = function (rowIndex: number, cellIndex: number, currentPlayer: string) {
  if (gameOver.value || board[rowIndex][cellIndex] !== '') {
    return
  }
  // 1. Apply the sign on the board
  board[rowIndex][cellIndex] = currentPlayer
  // 2. Check for winner
  checkForWinner(currentPlayer)
  // 3. Check for tie (no empty cells)
  checkForTie()
  // 4. Set game over or move to next turn
  if (winner.value || isTie.value) {
    gameOver.value = true
  } else {
    nextTurn()
  }
}

const checkForTie = function () {
  let tie = true
  board.forEach((row) => {
    if (row.some((cell) => cell === '')) {
      tie = false
      return
    }
  })
  isTie.value = tie
}

const checkForWinner = function (sign: string) {
  let result
  // Check all elements in one row
  board.forEach((row) => {
    if (row.every((cell) => cell === sign)) {
      winner.value = currentPlayer.value
      return
    }
  })

  // Check all elements in one column
  for (let i = 0; i <= 2; i++) {
    const column = [board[0][i], board[1][i], board[2][i]]
    result = column.every((cell) => cell === currentPlayer.value)
    if (result) {
      winner.value = currentPlayer.value
      return
    }
  }

  // Check diagonal elements
  const diagonalTopLeft = [board[0][0], board[1][1], board[2][2]]
  result = diagonalTopLeft.every((cell) => cell === currentPlayer.value)
  if (result) {
    winner.value = currentPlayer.value
    return
  } else {
    const diagonalTopLeft = [board[2][0], board[1][1], board[0][2]]
    result = diagonalTopLeft.every((cell) => cell === currentPlayer.value)
    if (result) {
      winner.value = currentPlayer.value
      return
    }
  }
}

const nextTurn = function () {
  currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
}

const resetGame = function () {
  cleanBoard()
  winner.value = null
  isTie.value = false
  currentPlayer.value = 'X'
  gameOver.value = false
}

const cleanBoard = function () {
  board = board.map((row) => {
    row.forEach((cell, cellIndex) => {
      row[cellIndex] = ''
    })
    return row
  })
}
</script>

<template>
  <h1>Tic Tac Toe</h1>

  <p v-if="gameOver">
    Game over! <span v-if="isTie">It is a tie!</span>
    <span v-else-if="winner">{{ winner }} won!</span>
  </p>
  <p v-else>Now playing: {{ currentPlayer }}</p>

  <div class="board">
    <div v-for="(row, rowIndex) in board">
      <div v-for="(cell, cellIndex) in row" class="cell">
        <Square-component
            :key="board[rowIndex][cellIndex]"
            :row-index="rowIndex"
            :cell-index="cellIndex"
            :sign="board[rowIndex][cellIndex]"
            :disabled="gameOver || board[rowIndex][cellIndex] !== ``"
            @click="makeMove(rowIndex, cellIndex, currentPlayer)"
        />
      </div>
    </div>
  </div>

  <button class="button" @click="resetGame">{{ gameOver ? `Play again!` : `Start over` }}</button>
</template>

<style lang="scss" scoped>
h1 {
  @apply text-8xl;
}

p {
  @apply text-base my-10;
  color: #0b7189;
}
.board {
  @apply flex flex-row gap-1 justify-center;

  .cell {
    @apply h-36 w-36 mb-1;
    background-color: #0b7189;
  }
}
.button {
  @apply border-0 rounded-full px-8 py-4 mt-10 text-base;
  background-color: #228cdb;
  color: #170a1c;
}
</style>