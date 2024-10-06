<template>
  <span
    class="bg-border"
    :class="
      cn(orientation === 'vertical' ? 'mx-2 w-[1px] min-w-[1px]' : 'my-2 h-[1px] min-h-[1px]', props.class)
    "></span>
</template>

<script setup>
import { cn } from '@/lib/utils'
const props = defineProps({
  orientation: {
    type: String,
    default: 'horizontal'
  },
  class: {
    type: String,
    default: ''
  }
})
</script>

<style lang="scss" scoped></style>
