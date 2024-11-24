<template>
  <div class="min-h-screen bg-gray-100 p-4">
    <div class="container mx-auto">
      <h1 class="text-3xl font-bold text-center mb-8">WebStorm to VSCode Snippet Converter</h1>
      
      <div class="grid grid-cols-2 gap-4">
        <div class="col-span-1">
          <div class="flex items-end mb-4 bg-white rounded-lg shadow p-4">
            <FilenameInput v-model="filename" />
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
          :language="'vscode-snippet'"
          :include-brackets="includeBrackets"
          @copy="copyToClipboard"
          @download="downloadJson"
          @update:include-brackets="includeBrackets = $event"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { useToast } from 'vue-toastification'
import { parseWebStormTemplate, convertToSnippets } from './utils/converter'
import { formatSnippetOutput, getFormattedContent, type SnippetOutput } from './utils/formatter'
import FilenameInput from './components/FilenameInput.vue'
import SourcePanel from './components/SourcePanel.vue'
import OutputPanel from './components/OutputPanel.vue'

const toast = useToast()
const sourceContent = ref('')
const snippets = ref<SnippetOutput>({})
const filename = ref('snippets')
const includeBrackets = ref(false)

const formattedOutput = computed(() => {
  return formatSnippetOutput(snippets, includeBrackets.value)
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

async function copyToClipboard() {
  try {
    const content = getFormattedContent(snippets.value, includeBrackets.value)
    await navigator.clipboard.writeText(content)
    toast.success('Copied to clipboard!')
  } catch (error) {
    console.error('Error copying to clipboard:', error)
    toast.error('Failed to copy to clipboard')
  }
}

function downloadJson() {
  const content = getFormattedContent(snippets.value, includeBrackets.value)
  
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