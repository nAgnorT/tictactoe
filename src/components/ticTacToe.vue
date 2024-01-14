<script setup>
import { ref, reactive } from "vue"

const winner = ref(null)
const isTie = ref(false)
const gameover = ref(false)
const currentPlayer = ref("X")
const player = ref(null)
const undoStack = reactive([])

let board = reactive([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""]
])

const randomNumber = () => {
  const random = Math.round(Math.random())
  player.value = random === 0 ? "Người chơi 1" : "Người chơi 2"
}

const checkTie = () => {
  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      if (!board[i][j]) {
        return false
      }
    }
  }
  return true
}

const checkWin = () => {
  const a = currentPlayer.value

  for (let i = 0; i < 3; i++) {
    if (board[i].every(cell => cell === a)) return true
    if (board.every(row => row[i] === a)) return true
  }

  if (board[0][0] === a && board[1][1] === a && board[2][2] === a) return true
  if (board[0][2] === a && board[1][1] === a && board[2][0] === a) return true

  return false
}

const undo = () => {
  if (winner.value) {
    return
  }
  if (isTie.value){
    return
  }
  if (undoStack.length === 0) {
    return
  }
  const lastitem = undoStack.length - 1
  const [row, col] = undoStack[lastitem]
  board[row][col] = ""
  currentPlayer.value = currentPlayer.value === "X" ? "O" : "X"
  player.value = player.value === "Người chơi 1" ? "Người chơi 2" : "Người chơi 1"
  undoStack.splice(lastitem, 1)
}

const play = (row, col) => {
  if (!player.value) {
    return
  }

  if (!board[row][col] && !winner.value) {
    undoStack.push([row, col])
    board[row][col] = currentPlayer.value
    if (checkWin()) {
      winner.value = `${currentPlayer.value} của ${player.value}`
    } else if (checkTie()) {
      isTie.value = true
    } else {
      currentPlayer.value = currentPlayer.value === "X" ? "O" : "X"
      player.value = player.value === "Người chơi 1" ? "Người chơi 2" : "Người chơi 1"
    }
  }
}

var reset = () => {
  winner.value = null
  currentPlayer.value = "X"
  gameover.value = false
  player.value = null
  isTie.value = false
  board = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""]
  ]
}
  </script>
<template>
  <div>
    <h1 class="">Tictactoe</h1>
    <div v-if="!player" class="">
      <button  @click="randomNumber" class="">Gacha</button> chọn người đi trước
    </div>

    <p v-if = "player" class="label" > Hiện tại: <span class="">{{ player }}</span> đánh <span class=""> {{ currentPlayer }} </span>
    </p>
    <div class="board">
      <div class="row" v-for="(row, rowIndex) of board" :key="rowIndex">
        <div class="cell" v-for="(cell, cellIndex) of row" :key="cellIndex"
          :class="{ 'cell-x': cell === 'X', 'cell-o': cell === 'O' }" :disabled="cell !== null"
          @click="play(rowIndex, cellIndex)">
          {{ cell }}
        </div>
      </div>
    </div>

    <div class="control">
      <p v-if="winner" class="label">{{ winner }} win!</p>
      <p v-else-if="isTie" class="label">Hoà</p>
      <div class="">
        <button @click="undo">Đi lại</button>
        <button @click="reset">Chơi lại</button>
      </div>

    </div>
  </div>
</template>
  

  