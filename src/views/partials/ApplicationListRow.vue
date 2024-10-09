<script setup>
import TableRow from '@/views/components/Table/TableRow.vue'
import FilledButton from '@/views/components/FilledButton.vue'
import { computed } from 'vue'
import moment from 'moment'
import router from '@/router/index.js'
import { camelCaseToSpacedCapitalized } from '@/vendor/utils.js'
import StatusBadge from '@/views/components/StatusBadge.vue'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { Icon } from '@iconify/vue'
import HealthIndicator from '../components/HealthIndicator.vue'

const props = defineProps({
  application: {
    type: Object,
    required: true
  },
  isVisible: {
    type: Boolean,
    default: true
  }
})

const createdAtFormatted = computed(() => {
  return moment(props.application.latestDeployment.createdAt).format('DD/MM/YYYY HH:mm')
})
const viewApplicationDetails = () => {
  router.push(`/application/${props.application.id}/deployments`)
}
</script>

<template>
  <tr v-show="isVisible">
    <TableRow align="left">
      <div class="text-sm font-medium">
        <span v-if="application.latestDeployment.upstreamType === 'git'">
          <Icon icon="lucide:git-branch" class="me-2 inline" />
          {{ application.name }}
        </span>
        <span v-else-if="application.latestDeployment.upstreamType === 'image'">
          <Icon icon="nonicons:docker-16" class="me-2 inline" />
          {{ application.name }}
        </span>
        <span v-else-if="application.latestDeployment.upstreamType === 'sourceCode'">
          <Icon icon="lucide:upload" class="me-2 inline" />
          {{ application.name }}</span
        >
      </div>
    </TableRow>
    <TableRow align="center" flex>
      <HealthIndicator :status="application.isSleeping ? 'sleeping' : application.realtimeInfo.HealthStatus" />
    </TableRow>
    <!-- Replicas -->
    <TableRow v-if="application.realtimeInfo.InfoFound" align="center">
      <div v-if="application.realtimeInfo.DeploymentMode === 'replicated'" class="text-sm">
        {{ application.realtimeInfo.RunningReplicas }} / {{ application.realtimeInfo.DesiredReplicas }}
      </div>
      <div v-else-if="application.realtimeInfo.DeploymentMode === 'global'" class="text-sm">Global</div>
    </TableRow>
    <TableRow v-else align="center">
      <div class="flex items-center justify-center">
        <Icon icon="lucide:circle-slash" class="me-2 h-4 w-4 text-muted-foreground" />
      </div>
    </TableRow>
    <!-- END Replicas -->
    <TableRow align="center" flex>
      <StatusBadge class="" v-if="application.latestDeployment.status === 'deployed'" type="success" small animate
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
      <StatusBadge class="" v-else-if="application.latestDeployment.status === 'pending'" type="warning" small
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
      <StatusBadge class="" v-else-if="application.latestDeployment.status === 'deployPending'" type="warning" small
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
      <StatusBadge class="" v-else-if="application.latestDeployment.status === 'deploying'" type="warning" small
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
      <StatusBadge class="" v-else-if="application.latestDeployment.status === 'failed'" type="danger" small
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
      <StatusBadge class="" v-else-if="application.latestDeployment.status === 'stopped'" type="secondary" small
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
      <StatusBadge class="" v-else-if="application.latestDeployment.status === 'stalled'" type="secondary" small
        >{{ camelCaseToSpacedCapitalized(application.latestDeployment.status) }}
      </StatusBadge>
    </TableRow>
    <TableRow align="center">
      <span class="text-sm"> {{ createdAtFormatted }} </span>
    </TableRow>
    <TableRow align="right" flex>
      <FilledButton :click="viewApplicationDetails" slim type="subtle">View Details</FilledButton>
    </TableRow>
  </tr>
</template>

<style scoped></style>
