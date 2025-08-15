<script setup lang="ts">
const emit = defineEmits<{
    (e: "resetGame"): void
}>();

</script>

<template>
    <button
        class="px-5 py-3 rounded-full text-white font-bold bg-[#268AFF] cursor-pointer transition-all ease-in hover:bg-[#1e6ecc]"
        @click="emit('resetGame')">
        Play again
    </button>
</template>