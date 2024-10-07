<template>
  <div v-if="status === 'sleeping'" class="flex flex-row items-center text-sm">
    <Icon icon="mingcute:sleep-line" class="me-2 h-4 w-4 text-yellow-500" />
    Sleeping
  </div>
  <div v-else-if="status === 'healthy'" class="flex flex-row items-center text-sm">
    <Icon icon="lucide:heart-pulse" class="me-2 h-4 w-4 text-green-500" />
    Healthy
  </div>
  <div v-else-if="status === 'unhealthy'" class="flex flex-row items-center text-sm">
    <Icon icon="lucide:heart-crack" class="me-2 h-4 w-4 text-red-500" />
    Unhealthy
  </div>
  <div v-else class="text-sm">
    <Icon icon="lucide:circle-slash" class="me-2 h-4 w-4 text-muted-foreground" />
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'
const props = defineProps({
  status: {
    type: String,
    required: true
  }
})
</script>

<style lang="scss" scoped></style>
