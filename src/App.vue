<template>
  <div class="min-h-screen bg-gray-100 p-4">
    <div class="container mx-auto">
      <h1 class="text-3xl font-bold text-center mb-8">WebStorm to VSCode Snippet Converter</h1>
      
      <div class="grid grid-cols-2 gap-4">
        <div class="col-span-1">
          <div class="flex items-end mb-4 bg-white rounded-lg shadow p-4">
            <FilenameInput v-model="filename" />
            <OutputToggle v-model="includeBrackets" />
          </div>
        </div>
        <div class="col-span-1"></div>

        <SourcePanel
          v-model="sourceContent"
          :template-count="templateCount"
          @clear="clearSource"
        />

        <OutputPanel
          :content="formattedOutput"
          @copy="copyToClipboard"
          @download="downloadJson"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { useToast } from 'vue-toastification'
import { parseWebStormTemplate, convertToSnippets } from './utils/converter'
import FilenameInput from './components/FilenameInput.vue'
import SourcePanel from './components/SourcePanel.vue'
import OutputPanel from './components/OutputPanel.vue'
import OutputToggle from './components/OutputToggle.vue'

const toast = useToast()
const sourceContent = ref('')
const snippets = ref<Record<string, any>>({})
const filename = ref('snippets')
const includeBrackets = ref(false)

const formattedOutput = computed(() => {
  if (!Object.keys(snippets.value).length) return ''
  
  const output = JSON.stringify(snippets.value, null, 2)
  if (includeBrackets.value) return output
  
  return output
    .split('\n')
    .slice(1, -1)
    .join('\n')
    .trim()
})

const templateCount = computed(() => {
  return Object.keys(snippets.value).length
})

function convertTemplate() {
  try {
    if (!sourceContent.value.trim()) {
      snippets.value = {}
      return
    }
    
    const templates = parseWebStormTemplate(sourceContent.value)
    snippets.value = convertToSnippets(templates)
  } catch (error) {
    console.error('Error converting template:', error)
    toast.error('Invalid template format')
  }
}

function clearSource() {
  sourceContent.value = ''
  snippets.value = {}
}

function copyToClipboard() {
  let content = formattedOutput.value
  if (!includeBrackets.value) {
    content = `{\n${content}\n}`
  }
  navigator.clipboard.writeText(content)
  toast.success('Copied to clipboard!')
}

function downloadJson() {
  let content = formattedOutput.value
  if (!includeBrackets.value) {
    content = `{\n${content}\n}`
  }
  
  const blob = new Blob([content], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = `${filename.value}.json`
  document.body.appendChild(a)
  a.click()
  document.body.removeChild(a)
  URL.revokeObjectURL(url)
}

// Watch for source content changes
watch(sourceContent, convertTemplate)
</script>