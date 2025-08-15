<script setup lang="ts">
import { ref } from 'vue';
import PlayerInput from './PlayerInput.vue';
import type { IPlayer } from '../models/IPlayers';

const emit = defineEmits<{
    (e: "addPlayers", players: IPlayer[]): void;
}>();

const playerOneInp = ref<string>("")
const playerTwoInp = ref<string>("")

const handleSubmit = () => {
    if (!playerOneInp.value.trim() || !playerTwoInp.value.trim()) {
        alert('Please, both, yes?')
        return
    }

    emit("addPlayers", [{ id: 1, name: playerOneInp.value, marker: "X" }, { id: 2, name: playerTwoInp.value, marker: "O" }])
    
    playerOneInp.value = "";
    playerTwoInp.value = "";
};
</script>

<template>
    <form @submit.prevent="handleSubmit" class="w-fit flex flex-col gap-y-8">
        <div class="flex flex-col gap-y-4">
            <PlayerInput label="Enter Player name for: X" v-model="playerOneInp" />
            <PlayerInput label="Enter Player name for: O" v-model="playerTwoInp" />
        </div>
        <button type="submit"
            class="px-5 py-3 rounded-full text-white font-bold bg-[#268AFF] cursor-pointer transition-all ease-in hover:bg-[#1e6ecc]">
            Start Game
        </button>
    </form>
</template>