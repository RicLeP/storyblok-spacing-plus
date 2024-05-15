<script setup>
import { useFieldPlugin } from '@storyblok/field-plugin/vue3'
import { computed, reactive, ref, watch } from 'vue'
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

watch(() => plugin.type,
  (type) => {
    if (type === 'loaded') {
      handleBreakpointClick(breakpoints.find(breakpoint => breakpoint.size === 's'))
    }
  },
)

</script>

<template>
  <div v-if="plugin.type === 'loaded'">
    <h2 class="title">Breakpoint</h2>

    <div class="button-group">
      <button
        v-for="breakpoint in breakpoints"
        :key="breakpoint.size"
        @click="handleBreakpointClick(breakpoint)"
        class="button-group__button"
        :class="{ 'button-group__button--active': selectedBreakpoint.size === breakpoint.size }"
      >
        {{ breakpoint.label }}
      </button>
    </div>

    <div class="box-model">
      <p class="box-model__title">Margin</p>

      <label class="select">
        <span class="select__label">Top:</span>

        <select v-model="selectedTopMargin" @change="handleSizeClick(selectedTopMargin, 'margin', 'top')"
                class="select__control">
          <option value="" disabled selected>Please select size</option>
          <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
        </select>
      </label>

      <div class="box-model__padding">
        <p class="box-model__title">Padding</p>

        <label class="select">
          <span class="select__label">Top:</span>

          <select v-model="selectedTopPadding" @change="handleSizeClick(selectedTopPadding, 'padding', 'top')"
                  class="select__control">
            <option value="" disabled selected>Please select size</option>
            <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
          </select>
        </label>

        <div class="box-model__content">
          Content
        </div>

        <label class="select">
          <span class="select__label">Bottom:</span>

          <select v-model="selectedBottomPadding" @change="handleSizeClick(selectedBottomPadding, 'padding', 'bottom')"
                  class="select__control">
            <option value="" disabled selected>Please select size</option>
            <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
          </select>
        </label>
      </div>

      <label class="select">
        <span class="select__label">Bottom:</span>

        <select v-model="selectedBottomMargin" @change="handleSizeClick(selectedBottomMargin, 'margin', 'bottom')"
                class="select__control">
          <option value="" disabled selected>Please select size</option>
          <option v-for="size in availableSizes" :key="size.size" :value="size">{{ size.label }}</option>
        </select>
      </label>
    </div>
  </div>
</template>

<style lang="postcss" scoped>

.title {
  margin: 0 0 7px;

  color: var(--sb_dark_blue_75);
  font-size: 12px;
  font-weight: normal;
  text-align: center;
}

.button-group {
  display: flex;

  justify-content: center;

  margin-bottom: 16px;

  border-radius: 5px;
}

.button-group__button {
  padding: 8px 10px;

  background-color: var(--sb_green_25);
  border: none;

  font-family: inherit;
  font-weight: 500;

  cursor: pointer;

  &:first-child {
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
  }

  &:last-child {
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
  }

  &:hover {
    background-color: var(--sb_green_50);
  }

  &:hover, &:active {
    background-color: var(--sb_green);

    color: #fff;
  }
}

.button-group__button--active {
  background-color: var(--sb_green);

  color: #fff;
}

.box-model {
  padding: 20px;

  background-color: #fff;
  border: 1px solid #dbdde2;
  border-radius: 8px;

  text-align: center;
}

.box-model__title {
  margin: 0 0 7px;

  color: var(--sb_dark_blue_75);
  font-size: 12px;
}

.box-model__padding {
  margin: 20px 0;
  padding: 20px 20px;

  background-color: var(--light_50);
  border-radius: 5px;
}

.box-model__content {
  margin: 20px 0;
  padding: 10px;

  background-color: var(--light);
  border-radius: 5px;

  color: var(--sb_dark_blue_75);
  font-size: 12px;
}

.select {
  display: flex;
  gap: 10px;

  align-items: center;
  justify-content: center;
}

.select__label {
  width: 43px;

  color: var(--sb_dark_blue_75);
  font-size: 12px;
  text-align: right;
}

.select__control {
  --webkit-appearance: none;
  --appearance: none;

  padding: 4px 8px;

  background-color: #fff;
  border: 1px solid var(--light_gray);
  border-radius: 5px;

  font-size: 12px;

  &:hover {
    border-color: var(--sb_green);

    cursor: pointer;
  }
}

</style>