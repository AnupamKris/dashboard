<script setup>
import { computed, ref, watch } from 'vue'
import { Combobox, ComboboxButton, ComboboxInput, ComboboxOption, ComboboxOptions } from '@headlessui/vue'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

const props = defineProps({
  value: {
    type: String,
    required: true
  },
  options: {
    type: Array,
    required: true
  },
  newOptionData: {
    type: Function,
    required: false,
    default: (e) => e
  },
  valueFromOption: {
    type: Function,
    required: false,
    default: (e) => e
  },
  titleFromOption: {
    type: Function,
    required: false,
    default: (e) => e
  },
  onChange: {
    type: Function,
    required: false,
    default: () => { }
  },
  onNewOption: {
    type: Function,
    required: false,
    default: () => { }
  }
})

const selectedOptionValue = ref(props.value)
const query = ref('')
const optionsIncludingQuery = computed(() => {
  if (!query.value) return props.options
  if (props.options.includes(query.value)) return props.options
  return [props.newOptionData(query.value), ...props.options]
})

const filteredOptions = computed(() =>
  !query.value
    ? optionsIncludingQuery.value
    : optionsIncludingQuery.value.filter((op) => {
      return props.titleFromOption(op).toLowerCase().includes(query.value.toLowerCase())
    })
)
const getDisplayValue = (i) => {
  for (let j = 0; j < props.options.length; j++) {
    if (props.valueFromOption(props.options[j]) === i) return props.titleFromOption(props.options[j])
  }
  if (query.value) {
    const data = props.newOptionData(query.value)
    if (props.valueFromOption(data) === i) return props.titleFromOption(data)
  }
  return ''
}

watch(selectedOptionValue, (newValue) => {
  props.onChange(newValue)
  if (newValue === 'new_group' && query.value) props.onNewOption(query.value)
})
</script>

<template>
  <Combobox v-model="selectedOptionValue">
    <div
      class="relative w-full overflow-hidden rounded-md border border-border shadow transition-all delay-75 hover:border-pri focus:border-pri focus:ring-pri sm:text-sm">
      <ComboboxInput :display-value="(i) => getDisplayValue(i)" @change="query = $event.target.value"
        placeholder="Start typing to filter..." class="w-full border-none text-sm focus:ring-0 bg-transparent" />
      <ComboboxButton class="absolute inset-y-0 right-0 flex items-center pr-2 ">
        <Icon icon="lucide:chevron-down" class="h-5 w-5" aria-hidden="true" />
      </ComboboxButton>
    </div>

    <ComboboxOptions
      class="scrollbox mt-1 max-h-40 overflow-y-auto overflow-x-hidden rounded-md border border-border shadow">
      <ComboboxOption v-for="op in filteredOptions" :key="valueFromOption(op)" :value="valueFromOption(op)"
        class="flex items-center justify-between px-3 py-2 text-sm font-medium hover:bg-sec hover:text-sec-foreground">
        <span>{{ titleFromOption(op) }}</span>
        <Icon icon="lucide:check" v-show="valueFromOption(op) === selectedOptionValue" />
      </ComboboxOption>
    </ComboboxOptions>
  </Combobox>
</template>
