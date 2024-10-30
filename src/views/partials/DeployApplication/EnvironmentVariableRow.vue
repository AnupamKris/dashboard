<script setup>
import FilledButton from '@/views/components/FilledButton.vue'
import Input from '@/views/components/Input.vue'
import TableRow from '@/views/components/Table/TableRow.vue'
import { Icon } from '@iconify/vue'
import { ref } from 'vue'

defineProps({
  variableKey: {
    type: String,
    required: true
  },
  variableName: {
    type: String,
    required: true
  },
  variableValue: {
    type: String,
    required: true
  },
  onVariableNameChange: {
    type: Function,
    required: true
  },
  onVariableValueChange: {
    type: Function,
    required: true
  },
  deleteVariable: {
    type: Function,
    required: true
  },
  index: {
    type: Number,
    required: false
  }
})

const valueHidden = ref(true)
</script>

<template>
  <tr :key="variableKey">
    <TableRow :class="{ '!py-1 !pt-3': index === 0, '!py-1': index !== 0 }">
      <input
        :key="`name-${variableKey}`"
        class="block w-full rounded-md border-border bg-background shadow-sm focus:border-pri focus:ring-pri sm:text-sm"
        placeholder="Environment Variable Name"
        type="text"
        v-bind:value="variableName"
        @input="(event) => onVariableNameChange(variableKey, event.target.value)" />
    </TableRow>
    <TableRow :class="{ '!py-1 !pt-3': index === 0, '!py-1': index !== 0 }">
      <div class="relative w-full">
        <Input
          :key="`value-${variableKey}`"
          placeholder="Environment Variable Value"
          :type="valueHidden ? 'password' : 'text'"
          v-bind:value="variableValue"
          @input="(event) => onVariableValueChange(variableKey, event.target.value)" />
        <div
          class="absolute inset-y-0 right-0 flex h-full w-12 cursor-pointer items-center px-1 py-1"
          @click.stop="valueHidden = !valueHidden">
          <div
            class="flex h-full w-full items-center justify-center rounded-sm bg-accent text-accent-foreground transition-all duration-200 hover:bg-accent/60">
            <Icon v-if="valueHidden" icon="lucide:eye-off" />
            <Icon v-else icon="lucide:eye" />
          </div>
        </div>
      </div>
    </TableRow>
    <TableRow align="right" class="flex" :class="{ '!py-1 !pt-3': index === 0, '!py-1': index !== 0 }">
      <FilledButton
        :key="`delete-${variableKey}`"
        :click="() => deleteVariable(variableKey)"
        class="w-full"
        type="danger">
        <font-awesome-icon class="mr-2" icon="fa-solid fa-trash" />
        Delete
      </FilledButton>
    </TableRow>
  </tr>
</template>
