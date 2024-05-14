<script setup>
import { useFieldPlugin } from '@storyblok/field-plugin/vue3'
import { computed, onMounted, reactive, ref } from 'vue'
import { createFieldPlugin } from '@storyblok/field-plugin'

createFieldPlugin({
  onUpdateState: (response) => {
    if (response.data.content) {
      Object.assign(selectedSizes, response.data.content)
    }
  },
})

const plugin = useFieldPlugin()

// const plugin = useFieldPlugin({
//   validateContent: (content) => ({
//     content: typeof content === 'object' ? content : selectedSizes,
//   }),
// })


const breakpoints = [
  { size: 's', label: 'Small' },
  { size: 'm', label: 'Medium' },
  { size: 'l', label: 'Large' },
]

const sizes = [
  { size: 'inherit', label: 'Inherit' },
  { size: 'none', label: 'None' },
  { size: 's', label: 'Small' },
  { size: 'm', label: 'Medium' },
  { size: 'l', label: 'Large' },
]

const availableSizes = computed(() => {
  if (selectedBreakpoint.value.size === 's') {
    return sizes.filter(size => size.size !== 'inherit')
  }
  return sizes
})

const selectedBreakpoint = ref(breakpoints[0])
const selectedTopMargin = ref(null)
const selectedBottomMargin = ref(null)
const selectedTopPadding = ref(null)
const selectedBottomPadding = ref(null)

// Create a reactive object to hold the selected sizes for each breakpoint
const selectedSizes = reactive({
  s: { margin: { top: 'none', bottom: 'none' }, padding: { top: 'none', bottom: 'none' } },
  m: { margin: { top: 'inherit', bottom: 'inherit' }, padding: { top: 'inherit', bottom: 'inherit' } },
  l: { margin: { top: 'inherit', bottom: 'inherit' }, padding: { top: 'inherit', bottom: 'inherit' } },
})

const handleBreakpointClick = (breakpoint) => {
  selectedBreakpoint.value = breakpoint
  selectedTopMargin.value = sizes.find(size => size.size === selectedSizes[breakpoint.size].margin.top)
  selectedBottomMargin.value = sizes.find(size => size.size === selectedSizes[breakpoint.size].margin.bottom)
  selectedTopPadding.value = sizes.find(size => size.size === selectedSizes[breakpoint.size].padding.top)
  selectedBottomPadding.value = sizes.find(size => size.size === selectedSizes[breakpoint.size].padding.bottom)
}

const handleSizeClick = (size, type, side) => {
  selectedSizes[selectedBreakpoint.value.size][type][side] = size.size

  handleSave()
}

const handleSave = () => {
  plugin.actions.setContent(selectedSizes)
}

onMounted(() => {
  // TODO - improve this - this is a workaround to set the initial selected breakpoint
  setTimeout(() => {
    handleBreakpointClick(breakpoints.find(breakpoint => breakpoint.size === 's'))
  }, 100)
})

</script>

<template>
  <button v-for="breakpoint in breakpoints" :key="breakpoint.size" @click="handleBreakpointClick(breakpoint)">
    {{ breakpoint.label }}
  </button>

  <h2>Selected breakpoint: {{ selectedBreakpoint.label }}</h2>

  <h2>Selected size for top margin:</h2>
  <select v-model="selectedTopMargin" @change="handleSizeClick(selectedTopMargin, 'margin', 'top')">
    <option value="" disabled selected>Please select size</option>
    <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
  </select>

  <h2>Selected size for bottom margin:</h2>
  <select v-model="selectedBottomMargin" @change="handleSizeClick(selectedBottomMargin, 'margin', 'bottom')">
    <option value="" disabled selected>Please select size</option>
    <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
  </select>

  <h2>Selected size for top padding:</h2>
  <select v-model="selectedTopPadding" @change="handleSizeClick(selectedTopPadding, 'padding', 'top')">
    <option value="" disabled selected>Please select size</option>
    <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
  </select>

  <h2>Selected size for bottom padding:</h2>
  <select v-model="selectedBottomPadding" @change="handleSizeClick(selectedBottomPadding, 'padding', 'bottom')">
    <option value="" disabled selected>Please select size</option>
    <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
  </select>

1
  <pre>
  </pre>
2

  <pre>
    {{ JSON.stringify(selectedSizes, null, 2) }}
  </pre>
</template>