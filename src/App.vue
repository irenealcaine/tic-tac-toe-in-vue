<script setup>
import { ref, computed, watchEffect } from "vue";

const player = ref("X");
const board = ref([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);
const playerXWins = ref(0);
const playerOWins = ref(0);

const CalculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};

const winner = computed(() => CalculateWinner(board.value.flat()));

const MakeMove = (x, y) => {
  if (winner.value) return;

  if (board.value[x][y] !== "") return;

  board.value[x][y] = player.value;

  player.value = player.value === "X" ? "O" : "X";
};

const ResetGame = () => {
  board.value = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ];
  player.value = "X";
};

const isBoardFull = computed(() => {
  for (let row of board.value) {
    for (let cell of row) {
      if (cell === "") {
        return false;
      }
    }
  }
  return true;
});

watchEffect(() => {
  if (winner.value === "X") {
    playerXWins.value++;
  } else if (winner.value === "O") {
    playerOWins.value++;
  }
});
</script>

<template>
  <main class="font-mono pt-8 text-center bg-gray-800 min-h-screen text-white">
    <h1
      class="mb-8 text-5xl font-bold uppercase underline decoration-green-500"
    >
      Tic Tac Toe
    </h1>
    <h2 class="text-x1 mb-4" v-if="!winner && !isBoardFull">
      Player {{ player }}'s turn
    </h2>

    <div class="flex flex-col items-center mb-8">
      <div v-for="(row, x) in board" :key="x" class="flex">
        <div
          v-for="(cell, y) in row"
          :key="y"
          @click="MakeMove(x, y)"
          :class="`border-2 border-green-500 w-20 md:w-28 h-20 md:h-28 hover:bg-gray-700 flex items-center justify-center material-icons-outlined text-6xl md:text-8xl cursor-pointer transition duration-200 ${
            cell === 'X' ? 'text-pink-500' : 'text-blue-400'
          }`"
        >
          {{ cell === "X" ? "close" : cell === "O" ? "circle" : "" }}
        </div>
      </div>
    </div>

    <h2
      v-if="winner"
      :class="`text-5xl font-bold mb-8 ${
        winner === 'X' ? 'text-pink-500' : 'text-blue-400'
      }`"
    >
      Player {{ winner }} wins!
    </h2>
    <h2
      v-if="isBoardFull && !winner"
      class="text-5xl text-green-500 font-bold mb-8"
    >
      It's a tie!
    </h2>

    <div class="flex flex-col md:flex-row md:gap-8 justify-center">
      <div>
        Player X wins:
        <span class="text-3xl text-pink-500">{{ playerXWins }}</span>
      </div>
      <div>
        Player O wins:
        <span class="text-3xl text-blue-400">{{ playerOWins }}</span>
      </div>
    </div>

    <button
      @click="ResetGame"
      v-if="winner || isBoardFull"
      class="px-16 py-2 my-4 text-3xl bg-red-700 rounded font-bold hover:bg-red-900 duration-200"
    >
      Reset
    </button>
  </main>
</template>

<style></style>
