<script setup>
import PageBar from '@/views/components/PageBar.vue'
import FilledButton from '@/views/components/FilledButton.vue'
import { useRouter } from 'vue-router'
import TableHeader from '@/views/components/Table/TableHeader.vue'
import TableMessage from '@/views/components/Table/TableMessage.vue'
import Table from '@/views/components/Table/Table.vue'
import { useQuery } from '@vue/apollo-composable'
import gql from 'graphql-tag'
import { computed, ref } from 'vue'
import { useToast } from 'vue-toastification'
import ApplicationListRow from '@/views/partials/ApplicationListRow.vue'
import ProjectListRow from '@/views/partials/ProjectListRow.vue'
import { Icon } from '@iconify/vue'

const router = useRouter()
const toast = useToast()

const deployNewApplication = () => {
  router.push('/deploy/application')
}

const installApplicationFromAppStore = () => {
  router.push({ name: 'App Store' })
}

const {
  result: applicationsResult,
  refetch: refetchApplications,
  loading: isApplicationsLoading,
  onError: onApplicationsError
} = useQuery(
  gql`
    query {
      applications(includeGroupedApplications: false) {
        id
        name
        replicas
        isSleeping
        realtimeInfo {
          InfoFound
          DesiredReplicas
          RunningReplicas
          DeploymentMode
          HealthStatus
        }
        latestDeployment {
          status
          upstreamType
          createdAt
        }
      }
    }
  `,
  null,
  {
    pollInterval: 60000
  }
)

onApplicationsError((err) => {
  toast.error(err.message)
})

const {
  result: applicationGroupsResult,
  refetch: refetchApplicationGroups,
  loading: isApplicationGroupsLoading,
  onError: onApplicationGroupsError
} = useQuery(
  gql`
    query {
      applicationGroups {
        id
        name
        logo
        applications {
          realtimeInfo {
            InfoFound
            DeploymentMode
            DesiredReplicas
            RunningReplicas
            HealthStatus
          }
        }
      }
    }
  `,
  null,
  {
    pollInterval: 60000
  }
)

onApplicationGroupsError((err) => {
  toast.error(err.message)
})

const refreshData = () => {
  refetchApplications()
  refetchApplicationGroups()
}

const applications = computed(() => applicationsResult.value?.applications ?? [])
const applicationGroups = computed(() => applicationGroupsResult.value?.applicationGroups ?? [])

const tabs = [
  { name: 'Applications', path: '/applications' },
  { name: 'Projects', path: '/applications' }
]

const currentTab = ref('Applications')
</script>

<template>
  <!-- Deploy Apps Page bar   -->
  <PageBar :tabs="tabs" v-model="currentTab">
    <template v-slot:title>Deployed Services</template>
    <template v-slot:subtitle>Manage your deployed services</template>
    <template v-slot:buttons>
      <FilledButton :click="deployNewApplication" type="primary" class="h-8 w-10">
        <Icon icon="lucide:plus" class="h-5 w-5 p-0" />
      </FilledButton>
      <FilledButton :click="installApplicationFromAppStore" type="primary" class="h-8 w-10">
        <Icon icon="lucide:store" class="h-5 w-5 p-0" />
      </FilledButton>
      <FilledButton type="outline" :click="refreshData" class="h-8 w-10">
        <Icon
          icon="lucide:refresh-ccw"
          :class="{
            'animate-spin ': isApplicationsLoading || isApplicationGroupsLoading
          }" />
      </FilledButton>
    </template>
  </PageBar>
  <section class="mx-auto w-full max-w-7xl p-4">
    <template v-if="currentTab === 'Applications'">
      <p class="mt-6 text-sm font-medium flex items-center">
        <Icon icon="lucide:layout-grid" class="mr-2 w-5 h-5" />
        <h1 class="text-lg font-medium">Deployed Applications</h1>
      </p>

      <!-- Applications Table -->
      <Table class="mt-4">
        <template v-slot:header>
          <TableHeader align="left">Application Name</TableHeader>
          <TableHeader align="center">Health Status</TableHeader>
          <TableHeader align="center">Replicas</TableHeader>
          <TableHeader align="center">Deploy Status</TableHeader>
          <TableHeader align="center">Last Deployment</TableHeader>
          <TableHeader align="right">View Details</TableHeader>
        </template>
        <template v-slot:message>
          <TableMessage v-if="applications.length === 0">
            No deployed applications found.<br />
            Click on the "Deploy App" button to deploy a new application.
          </TableMessage>
          <TableMessage v-if="isApplicationsLoading && applications.length === 0">
            Loading deployed applications...
          </TableMessage>
        </template>
        <template v-slot:body>
          <ApplicationListRow v-for="application in applications" :key="application.id" :application="application" />
        </template>
      </Table>
    </template>
    <template v-else>
      <!--  Deployed Projects Bar   -->
      <p class="mt-6 text-sm font-medium flex items-center">
        <Icon icon="ph:stack" class="mr-2 w-5 h-5" />
        <h1 class="text-lg font-medium">Deployed Projects</h1>
      </p>

      <!-- Projects Table -->
      <Table v-if="currentTab === 'Projects'" class="mt-4">
        <template v-slot:header>
          <TableHeader align="left">Project Name</TableHeader>
          <TableHeader align="center">Health Status</TableHeader>
          <TableHeader align="center">Total Services</TableHeader>
          <TableHeader align="center">Healthy Services</TableHeader>
          <TableHeader align="center">Unhealthy Services</TableHeader>
          <TableHeader align="right">View Details</TableHeader>
        </template>
        <template v-slot:message>
          <TableMessage v-if="applicationGroups.length === 0"> No projects found.</TableMessage>
          <TableMessage v-if="isApplicationGroupsLoading && applicationGroups.length === 0">
            Loading deployed projects...
          </TableMessage>
        </template>
        <template v-slot:body>
          <ProjectListRow v-for="group in applicationGroups" :key="group.id" :project="group" />
        </template>
      </Table>
    </template>
  </section>
</template>

<style scoped></style>
