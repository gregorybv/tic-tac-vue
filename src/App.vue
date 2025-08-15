<script setup lang="ts">
import { ref } from 'vue';
import Cell from './components/Cell.vue';
import type { ICellData } from './models/ICellData';
import type { IPlayer } from './models/IPlayers';
import PlayerForm from './components/PlayerForm.vue';
import RestartButton from './components/RestartButton.vue';
import XMarker from './components/XMarker.vue';
import OMarker from './components/OMarker.vue';

const grid = ref<ICellData[] | null[]>([
	null, null, null,
	null, null, null,
	null, null, null
])

const playAgain = ref<boolean>(false)

const players = ref<IPlayer[]>([])

const currentPlayer = ref<IPlayer | null>(null)

const gameResult = ref<'X' | 'O' | 'draw' | null>(null);

const checkMatch = (grid: ICellData[] | null[], a: number, b: number, c: number): boolean | null => {
	return grid[a] && grid[a].marker === grid[b]?.marker && grid[a].marker === grid[c]?.marker;
}

const checkWinner = (grid: ICellData[] | null[]): string | null => {
	const winningConditions = [
		[0, 1, 2], [3, 4, 5], [6, 7, 8],
		[0, 3, 6], [1, 4, 7], [2, 5, 8],
		[0, 4, 8], [2, 4, 6]
	];

	for (let i = 0; i < winningConditions.length; i++) {
		const [a, b, c] = winningConditions[i];

		if (checkMatch(grid, a, b, c)) {
			return grid[a]?.marker!
		}
	}

	return null;
}

const makeMove = (index: number) => {
 if (playAgain.value) return;
 if (grid.value[index] !== null) return;

 grid.value[index] = {
  playerID: currentPlayer.value!.id,
  marker: currentPlayer.value!.marker
 };

 const winnerMarker = checkWinner(grid.value);

 if (winnerMarker) {
  playAgain.value = true;
  gameResult.value = winnerMarker as 'X' | 'O';
  return;
 }

 if (grid.value.every(cell => cell !== null)) {
  playAgain.value = true;
  gameResult.value = 'draw';
  return;
 }

 currentPlayer.value = currentPlayer.value!.marker === 'X'
  ? players.value.find(p => p.marker === 'O')!
  : players.value.find(p => p.marker === 'X')!;
}

const addPlayers = (playersToAdd: IPlayer[]) => {
	if (playersToAdd.length === 2) {
		players.value = playersToAdd;

		currentPlayer.value = playersToAdd[0]
	}
}

const resetGame = () => {
 grid.value = Array(9).fill(null);
 currentPlayer.value = players.value[0];
 playAgain.value = false;
 gameResult.value = null;
}

</script>

<template>
	<PlayerForm v-if="players.length !== 2" @addPlayers="addPlayers" />
	<section v-else class="flex flex-col items-center gap-12">
  <p v-if="!playAgain" class="px-5 py-2 text-2xl font-semibold rounded-lg bg-gray-500">
   {{ currentPlayer?.name }}'s TURN ({{ currentPlayer?.marker }})
  </p>

  <p v-else-if="gameResult === 'draw'" class="px-5 py-2 text-2xl font-semibold rounded-lg bg-yellow-500">
   It's a draw!
  </p>

  <p v-else class="px-5 py-2 text-2xl font-semibold rounded-lg bg-green-500">
   Player {{ players.find(p => p.marker === gameResult)?.name }} WINS!!!
  </p>

		<div class="w-fit p-3 grid grid-cols-3 grid-rows-3 gap-3 rounded-3xl bg-[#9ee712] shadow-lg">
			<Cell 
				v-for="(cell, index) in grid"
				:key="index"
				:cell="cell"
				:gameOver="playAgain"
				:cellPosition="index"
				@place-marker="makeMove"
			>
				<XMarker v-if="cell?.marker === 'X'" />
				<OMarker v-if="cell?.marker === 'O'" />
			</Cell>
		</div>

		<RestartButton v-if="playAgain" @reset-game="resetGame" />
	</section>
</template>