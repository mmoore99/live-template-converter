<template>
  <div class="bg-white rounded-lg shadow p-4">
    <div class="flex justify-between items-center mb-4">
      <div>
        <h2 class="text-xl font-semibold">WebStorm Template</h2>
        <p class="text-sm text-gray-600">{{ templateCount }} templates loaded</p>
      </div>
      <button 
        @click="$emit('clear')" 
        class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700"
      >
        Clear
      </button>
    </div>
    <textarea 
      :value="modelValue"
      @input="$emit('update:modelValue', ($event.target as HTMLTextAreaElement).value)"
      class="w-full h-[calc(100vh-300px)] p-4 font-mono text-sm border rounded"
      placeholder="Paste your WebStorm template XML here..."
    ></textarea>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  modelValue: string
  templateCount: number
}>();
defineEmits<{
  (e: 'update:modelValue', value: string): void
  (e: 'clear'): void
}>();
</script>