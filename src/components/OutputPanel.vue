<template>
  <div class="bg-white rounded-lg shadow p-4">
    <div class="flex justify-between mb-4">
      <h2 class="text-xl font-semibold">VSCode Snippets</h2>
      <div class="space-x-2">
        <button 
          @click="$emit('copy')" 
          class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
        >
          Copy
        </button>
        <button 
          @click="$emit('download')" 
          class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700"
        >
          Download
        </button>
      </div>
    </div>
    <pre class="w-full h-[calc(100vh-300px)] p-4 font-mono text-sm border rounded overflow-auto">{{ content }}</pre>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  content: string
}>();
defineEmits<{
  (e: 'copy'): void
  (e: 'download'): void
}>();
</script>