<script setup>
import { reactive, ref } from 'vue'
import { useAuthStore } from '@/store/auth.js'
import router from '@/router/index.js'
import FilledButton from '@/views/components/FilledButton.vue'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { Icon } from '@iconify/vue'
import Input from '../components/Input.vue'

const username = ref('')
const password = ref('')
const totp = ref('')
const authenticationStatus = reactive({
  visible: false,
  success: false,
  message: '',
  totp_required: false
})
const authStore = useAuthStore()

const login = async () => {
  let res = await authStore.Login(username.value, password.value, totp.value)
  if (res.totp_required) {
    authenticationStatus.totp_required = res.totp_required
  } else {
    authenticationStatus.success = res.success
    authenticationStatus.message = res.message
    authenticationStatus.visible = true
    authenticationStatus.totp_required = authenticationStatus.totp_required || res.totp_required
    if (res.success) {
      // check if `redirect` is in the query
      if (router.currentRoute.value.query.redirect) {
        await router.push(router.currentRoute.value.query.redirect)
        return
      }
      window.open(router.resolve({ name: 'Applications' }).href, '_self')
    }
  }
}
</script>

<template>
  <div class="flex h-full w-full flex-row">
    <!--  Content  -->
    <div class="relative flex h-full min-w-[60vw] select-none flex-col items-center pt-52 bg-sec dark:bg-pri/5">
      <!--   Logo with title/subtitle   -->
      <div class="flex w-fit flex-row items-center justify-center gap-2">
        <img src="@/assets/images/logo.png" class="w-14" alt="swiftwave logo" />
        <div class="flex flex-col items-start justify-between">
          <p class="font-prompt text-3xl text-sec-foreground">swiftwave</p>
          <p class="font-prompt text-base text-sec-foreground">open source paas</p>
        </div>
      </div>
      <!--    Heading  -->
      <p class="mt-32 font-comfortaa text-5xl text-sec-foreground"><span class="text-primary-600">Simple
          Lightweight</span>&nbsp;PaaS</p>
      <p class="mt-6 font-comfortaa text-5xl text-sec-foreground">for self-hosting</p>
      <!--   Button panel   -->
      <div class="absolute bottom-0 left-0 right-0 flex flex-row flex-wrap items-center justify-center gap-3 pb-6">
        <!--        <p class="w-full text-center">Hemlo bro</p>-->
        <FilledButton type="primary">
          <a class="action-btn" target="_blank" href="https://github.com/swiftwave-org/swiftwave">
            <Icon icon="simple-icons:github" class="icon" />
            Github
          </a>
        </FilledButton>
        <FilledButton type="primary">
          <a class="action-btn" target="_blank" href="https://github.com/swiftwave-org/swiftwave/issues/new/choose">
            <Icon icon="lucide:bug" class="icon h-4 w-4" />
            Report Bug
          </a>
        </FilledButton>
        <FilledButton type="primary">
          <a class="action-btn" target="_blank" href="https://slack.swiftwave.org/">
            <Icon icon="lucide:users" class="icon h-4 w-4" />
            Join our community
          </a>
        </FilledButton>
        <FilledButton type="primary">
          <a class="action-btn" target="_blank" href="mailto:support@swiftwave.org">
            <Icon icon="lucide:mail" class="icon h-4 w-4" />
            Reach out to team
          </a>
        </FilledButton>
        <FilledButton type="primary">
          <a class="action-btn" target="_blank" href="https://swiftwave.org/docs/support_us/">
            <Icon icon="lucide:handshake" class="icon h-4 w-4" />
            Support <b>Swiftwave</b>
          </a>
        </FilledButton>
      </div>
    </div>
    <!--   Login form -->
    <div class="flex h-full w-full flex-col items-center justify-center px-6 py-12 lg:px-8 bg-background">
      <p class="w-fit text-5xl text-primary-600">
        <Icon icon="lucide:fingerprint" class="h-16 w-16" />
      </p>
      <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-sm">
        <!-- Alert  -->
        <div v-if="authenticationStatus.visible" :class="{
          'border-red-500 bg-red-50': !authenticationStatus.success,
          'border-green-500 bg-green-50': authenticationStatus.success
        }" class="mb-5 rounded border-s-4 p-4" role="alert">
          <strong :class="{
            'text-red-800': !authenticationStatus.success,
            'text-green-800': authenticationStatus.success
          }" class="block font-medium">{{ authenticationStatus.message }}</strong>
        </div>

        <!--   Login Form   -->
        <form class="space-y-4" @keydown.enter.prevent="login">
          <div>
            <label class="block text-sm font-medium leading-6 text-muted-foreground" for="username">Username</label>
            <div class="mt-1">
              <Input id="username" v-model="username" autocomplete="username" name="username"
                placeholder="Enter username" required type="text" />
            </div>
          </div>
          <div>
            <label class="block text-sm font-medium leading-6 text-muted-foreground" for="password">Password</label>
            <div class="mt-1">
              <Input id="password" v-model="password" autocomplete="current-password" placeholder="Enter password"
                required type="password" />
            </div>
          </div>
          <div v-if="authenticationStatus.totp_required">
            <label class="block text-sm font-medium leading-6 text-muted-foreground" for="2fa_code">Provide 2FA
              Code</label>
            <div class="mt-2">
              <v-otp-input :num-inputs="6" input-classes="otp-input" :style="{ justifyContent: 'space-between' }"
                :placeholder="['*', '*', '*', '*', '*', '*']" v-model:value="totp" @on-change="(v) => (totp = v)" />
            </div>
          </div>
          <div class="py-2">
            <FilledButton :click="login" class="w-full"> Sign in</FilledButton>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped>
@font-face {
  font-family: 'Comfortaa';
  src: url('@/assets/fonts/Comfortaa-Regular.ttf');
}

@font-face {
  font-family: 'Prompt';
  src: url('@/assets/fonts/Prompt-Regular.ttf');
}

/* .action-btn {
  @apply cursor-pointer rounded-lg border border-secondary-300 bg-white px-4 py-1 font-prompt text-base no-underline shadow-sm hover:bg-secondary-100 focus:outline-none;

  .icon {
    @apply mr-1 text-sm;
  }
} */


.action-btn {
  @apply flex gap-1 items-center;
}
</style>
