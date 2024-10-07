<script setup>
import TableRow from '@/views/components/Table/TableRow.vue'
import FilledButton from '@/views/components/FilledButton.vue'
import { computed } from 'vue'
import router from '@/router/index.js'
import HealthIndicator from '../components/HealthIndicator.vue'

const props = defineProps({
  project: {
    type: Object,
    required: true
  },
  isVisible: {
    type: Boolean,
    default: true
  }
})

const overallHealthStatus = computed(() => {
  if (props.project.applications.length === 0) {
    return 'unknown'
  }
  const healthyApplications = props.project.applications.filter((app) => app.realtimeInfo.HealthStatus === 'healthy')
  if (healthyApplications.length === props.project.applications.length) {
    return 'healthy'
  }
  return 'unhealthy'
})

const healthyServiceCount = computed(() => {
  if (props.project.applications.length === 0) {
    return 0
  }
  return props.project.applications.filter((app) => app.realtimeInfo.HealthStatus === 'healthy').length
})

const unhealthyServiceCount = computed(() => {
  if (props.project.applications.length === 0) {
    return 0
  }
  return props.project.applications.filter((app) => app.realtimeInfo.HealthStatus === 'unhealthy').length
})

const viewApplicationGroupDetails = () => {
  router.push(`/application_group/${props.project.id}`)
}
</script>

<template>
  <tr v-show="isVisible">
    <TableRow align="left">
      <div class="flex items-center gap-2 text-sm font-medium">
        <img v-if="project.logo" :src="project.logo" class="h-4 w-4 rounded-sm" alt="logo" />
        {{ project.name }}
      </div>
    </TableRow>
    <TableRow align="center" flex>
      <HealthIndicator :status="overallHealthStatus" />
    </TableRow>
    <TableRow align="center">
      <div class="text-sm">{{ project.applications.length }}&nbsp;Services</div>
    </TableRow>
    <TableRow align="center" flex>
      <div class="text-sm text-success-500">{{ healthyServiceCount }}&nbsp;Healthy</div>
    </TableRow>
    <TableRow align="center">
      <div class="text-sm text-danger-500">{{ unhealthyServiceCount }}&nbsp;Unhealthy</div>
    </TableRow>
    <TableRow align="right" flex>
      <FilledButton :click="viewApplicationGroupDetails" slim type="primary">View Details</FilledButton>
    </TableRow>
  </tr>
</template>

<style scoped></style>
