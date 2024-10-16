<template>
  <input
    :type
    :placeholder
    v-model="value"
    class="block w-full rounded-md border-border bg-background pe-14 shadow-sm focus:border-pri focus:ring-pri sm:text-sm" />
</template>

<script setup>
const props = defineProps({
  type: {
    type: String,
    default: 'text'
  },
  placeholder: {
    type: String,
    default: ''
  }
})

const value = defineModel()
</script>

<style lang="scss" scoped></style>
