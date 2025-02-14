<script setup lang="ts">
defineProps<{
    resultText: string;
}>();

const { copy, copied } = useClipboard();
</script>

<template>
    <div class="p-12 bg-gray-200 font-mono relative group">
        {{ resultText }}
        <button @click="copy(resultText)" class="absolute invisible group-hover:visible top-2 right-2 bg-gray-100 rounded-md p-1 text-gray-600 active:bg-gray-50">
            {{ copied ? 'copied' : 'copy' }}
        </button>
    </div>
</template>