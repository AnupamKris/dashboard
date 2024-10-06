<script setup>
import { useAuthStore } from '@/store/auth.js'
import { RouterLink, useRouter } from 'vue-router'
import Logo from '@/assets/images/logo.png'
import ChangePasswordModal from '@/views/partials/ChangePasswordModal.vue'
import { computed, onMounted, ref } from 'vue'
import SideBarOption from '@/views/partials/SideBarOption.vue'
import { useMutation } from '@vue/apollo-composable'
import gql from 'graphql-tag'
import { useToast } from 'vue-toastification'
import ModalDialog from '@/views/components/ModalDialog.vue'
import Separator from '@/views/components/Separator.vue'
import { Icon } from '@iconify/vue'

const authStore = useAuthStore()
const router = useRouter()
const toast = useToast()

const isChangePasswordModalOpen = ref(false)
const swVersion = ref('')
const openChangePasswordModal = () => {
  isChangePasswordModalOpen.value = true
}
const closeChangePasswordModal = () => {
  isChangePasswordModalOpen.value = false
}
const isShowSideBar = computed(() => {
  if (!authStore.IsLoggedIn) {
    return false
  } else {
    return !['Download Persistent Volume Backup', 'Maintenance', 'Setup'].includes(router.currentRoute.value.name)
  }
})

const logoutWithConfirmation = () => {
  if (confirm('Are you sure you want to logout?')) {
    authStore.Logout()
  }
}

const fetchSWVersion = () => {
  if (authStore.IsLoggedIn) {
    authStore.fetchSWVersion().then((v) => {
      swVersion.value = v
    })
  }
}

onMounted(() => {
  fetchSWVersion()
  const intervalId = setInterval(() => {
    if (authStore.IsLoggedIn) {
      if (swVersion.value === '') {
        fetchSWVersion()
      } else {
        clearInterval(intervalId)
      }
    }
  }, 2000)
})

// Restart system
const timeCount = ref(5)

const isSystemRestartModalOpen = ref(false)
const {
  mutate: restartSystem,
  onDone: onRestartSystemDone,
  onError: onRestartSystemError
} = useMutation(gql`
  mutation {
    restartSystem
  }
`)

onRestartSystemError((error) => {
  toast.error(error.message)
})

onRestartSystemDone((val) => {
  if (val.data.restartSystem) {
    toast.success('System restart requested')
    isSystemRestartModalOpen.value = true
    startCountDown()
  } else {
    toast.error('System restart failed')
  }
})

const systemRestart = () => {
  if (confirm('Are you sure you want to restart swiftwave ?\nYour deployed applications will not face any downtime.')) {
    restartSystem()
  }
}

const startCountDown = () => {
  const interval = setInterval(() => {
    timeCount.value--
    if (timeCount.value === 0) {
      clearInterval(interval)
      isSystemRestartModalOpen.value = false
      router.push({ name: 'Maintenance', query: { redirect: router.currentRoute.value.path } })
    }
  }, 1000)
}
</script>

<template>
  <aside
    v-if="isShowSideBar"
    class="scrollbox flex h-screen w-14 flex-col overflow-y-hidden border-r px-2 pb-2 pt-6 transition-all duration-300 hover:w-72">
    <div class="flex w-full items-center justify-center">
      <RouterLink to="/" class="flex w-full items-center">
        <img :src="Logo" alt="logo" class="ml-1 w-7" />

        <span class="nav-text w-fit pl-0 font-semibold"
          >Swiftwave
          <p class="text-muted-foreground text-xs">v{{ swVersion }}</p>
        </span>
      </RouterLink>
    </div>
    <div class="mt-6 flex w-full flex-1 flex-col justify-between">
      <nav class="flex w-full flex-col items-center justify-start">
        <Separator class="w-2/3" />
        <RouterLink class="router-link" to="/deploy/app-store">
          <Icon icon="lucide:store" class="h-5 w-5" />
          <span class="nav-text">App Store</span>
        </RouterLink>

        <RouterLink class="router-link" to="/deploy/application">
          <Icon icon="lucide-lab:layout-grid-plus" class="h-5 w-5" />
          <span class="nav-text">Deploy App</span>
        </RouterLink>
        <RouterLink class="router-link" to="/deploy/stack">
          <Icon icon="ph:stack" class="h-5 w-5" />
          <span class="nav-text">Deploy Stack</span>
        </RouterLink>
        <Separator class="w-2/3" />
        <RouterLink class="router-link" to="/applications">
          <Icon icon="lucide:box" class="h-5 w-5" />
          <span class="nav-text">Applications</span>
        </RouterLink>
        <RouterLink class="router-link" to="/persistent-volumes">
          <Icon icon="lucide:hard-drive" class="h-5 w-5" />
          <span class="nav-text">Persistent Volumes</span>
        </RouterLink>
        <Separator class="w-2/3" />
        <RouterLink class="router-link" to="/domains">
          <Icon icon="lucide:globe" class="h-5 w-5" />
          <span class="nav-text">Domains</span>
        </RouterLink>
        <RouterLink class="router-link" to="/ingress-rules">
          <Icon icon="lucide:waypoints" class="h-5 w-5" />
          <span class="nav-text">Ingress Rules</span>
        </RouterLink>
        <RouterLink class="router-link" to="/redirect-rules">
          <Icon icon="lucide:send" class="h-5 w-5" />
          <span class="nav-text">Redirect Rules</span>
        </RouterLink>
        <Separator class="w-2/3" />

        <RouterLink class="router-link" to="/git-credentials">
          <Icon icon="lucide:fingerprint" class="h-5 w-5" />
          <span class="nav-text">Git Credentials</span>
        </RouterLink>
        <!-- TODO: Combine with git credentials -->
        <!-- <RouterLink class="router-link" to="/image-registry-credentials">
          <font-awesome-icon icon="fa-solid fa-cloud" />
          <span class="nav-text">Image Reg Credentials</span>
        </RouterLink> -->

        <RouterLink class="router-link" to="/app_auth/basic_authentication">
          <Icon icon="lucide:shield-check" class="h-5 w-5" />
          <span class="nav-text">Basic Authentication</span>
        </RouterLink>

        <Separator class="w-2/3" />

        <RouterLink class="router-link" to="/servers">
          <Icon icon="lucide:server" class="h-5 w-5" />
          <span class="nav-text">Server list</span>
        </RouterLink>

        <RouterLink class="router-link" to="/logs">
          <Icon icon="lucide:square-activity" class="h-5 w-5" />
          <span class="nav-text">System Logs</span>
        </RouterLink>
        <RouterLink class="router-link" to="/setup?update=1">
          <Icon icon="lucide:wrench" class="h-5 w-5" />
          <span class="nav-text">System Config</span>
        </RouterLink>

        <RouterLink class="router-link" to="/users">
          <Icon icon="lucide:users" class="h-5 w-5" />
          <span class="nav-text">Manage Users</span>
        </RouterLink>
        <div
          @click="systemRestart"
          class="flex transform cursor-pointer items-center rounded-lg px-3 py-2 text-gray-200 transition-colors duration-300 hover:bg-gray-100 hover:text-gray-700">
          <font-awesome-icon icon="fa-solid fa-power-off" />
          <span class="nav-text">System Restart</span>
        </div>

        <div
          class="flex transform cursor-pointer items-center rounded-lg px-3 py-2 text-gray-200 transition-colors duration-300 hover:bg-gray-100 hover:text-gray-700"
          @click="openChangePasswordModal">
          <font-awesome-icon icon="fa-solid fa-key" />
          <span class="nav-text">Change Password</span>
        </div>
        <a
          class="flex transform cursor-pointer items-center rounded-lg px-3 py-2 text-gray-200 transition-colors duration-300 hover:bg-gray-100 hover:text-gray-700"
          @click="logoutWithConfirmation">
          <font-awesome-icon icon="fa-solid fa-right-from-bracket" />
          <span class="nav-text">Logout</span>
        </a>
      </nav>
    </div>
    <div class="flex justify-between px-2 text-sm font-medium">
      <span>Auto-logout {{ authStore.sessionRelativeTimeoutStatus }}</span>
      <span> v{{ swVersion }}</span>
    </div>
    <ChangePasswordModal :is-modal-open="isChangePasswordModalOpen" :close-modal="closeChangePasswordModal" />
    <Teleport to="body">
      <!-- Modal for restart system -->
      <ModalDialog :is-open="isSystemRestartModalOpen" non-cancelable>
        <template v-slot:header>
          <span>🔌 Restarting System</span>
        </template>
        <template v-slot:body>
          <p class="mb-2">System restart has been requested.</p>
          <p>
            Redirecting to Maintenance Page in <b>{{ timeCount }}</b> seconds
          </p>
        </template>
      </ModalDialog>
    </Teleport>
  </aside>
</template>

<style scoped>
.scrollbox::-webkit-scrollbar {
  width: 12px;
}

.nav-text {
  @apply w-0 overflow-hidden text-ellipsis whitespace-nowrap pl-0 text-sm opacity-0 transition-all duration-300;
  /* transition-property: width, opacity, pl; */
}

aside:hover .nav-text {
  @apply pl-4 opacity-100;
  width: calc(100% - 4rem);
}

.router-link {
  /* @apply hover:bg-muted hover:text-foreground text-muted-foreground flex w-full transform items-center rounded-lg px-3 py-2; */
  @apply text-muted-foreground hover:bg-muted hover:text-foreground flex w-full items-center overflow-hidden rounded-lg py-2 pl-2;
}

.router-link-exact-active {
  @apply bg-pri text-background hover:bg-pri hover:text-background;
}

.scrollbox::-webkit-scrollbar-thumb {
  @apply rounded-full shadow-[inset_0_0_10px_10px_white];
  border: solid 3px transparent;
}
</style>
