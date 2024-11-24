<template>
  <div class="bg-white rounded-lg shadow p-4">
    <div class="flex justify-between items-center mb-4">
      <h2 class="text-xl font-semibold">VSCode Snippets</h2>
      <div class="flex items-center space-x-4">
        <OutputToggle
          v-model="localIncludeBrackets"
          @update:modelValue="$emit('update:include-brackets', $event)"
        />
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
    </div>
    <MonacoEditor
      v-model="editorContent"
      :language="language"
      :read-only="true"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import MonacoEditor from './MonacoEditor.vue'
import OutputToggle from './OutputToggle.vue'

const props = defineProps<{
  content: string
  language: string
  includeBrackets: boolean
}>()

const emit = defineEmits<{
  (e: 'copy'): void
  (e: 'download'): void
  (e: 'update:include-brackets', value: boolean): void
}>()

const editorContent = ref(props.content)
const localIncludeBrackets = ref(props.includeBrackets)

watch(() => props.content, (newValue) => {
  editorContent.value = newValue
})

watch(() => props.includeBrackets, (newValue) => {
  localIncludeBrackets.value = newValue
})
</script>