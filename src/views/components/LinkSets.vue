<template>
  <div class="flex items-center gap-2 w-fit">
    <div v-if="ingressRules.length > 0"
      class="deployment-head max-w-[40vw] flex items-center gap-1 border px-1 rounded-md h-9 bg-background" :class="{
        '!pr-1': ingressRules.length > 0
      }">
      <Icon icon="lucide:globe" class="ml-1" />
      <span v-for="(ingressRule, index) in ingressRules" :key="index" class="h-full py-1">
        <a :href="ingressRule.protocol +
          '://' +
          ((ingressRule.domain?.name || null) ?? 'proxy_server_ip') +
          ':' +
          ingressRule.port.toString()
          " target="_blank"
          class="has-popover rounded-md bg-pri px-1 py-1 min-w-5 min-h-5 text-pri-foreground flex items-center gap-1 h-full">
          <Icon icon="lucide:link" class="ml-1" />
          <p class="text-xs pr-1">Link {{ index + 1 }}</p>
          <div class="popover">
            {{
              ingressRule.protocol +
              '://' +
              ((ingressRule.domain?.name || null) ?? 'proxy_server_ip') +
              ':' +
              ingressRule.port.toString()
            }}
          </div>
        </a>
      </span>
    </div>
    <div v-else class="has-popover flex cursor-pointer gap-2 border p-1 rounded-md h-9">
      <div class="deployment-head flex flex-row items-center gap-2 p-1">
        <Icon icon="lucide:globe" class="" />
        <p class="text-warning-600 text-xs">Not Exposed</p>
        <RouterLink v-if="showAddLink" :to="{
          name: addLinkRoute,
          params: { id: $route.params.id }
        }" class="font-semibold hover:cursor-pointer hover:text-pri">
          <Icon icon="lucide:plus" class="h-4 w-4" />
        </RouterLink>
      </div>
      <div class="popover w-60">
        No Ingress Rules available. Please open the <b>application details</b> page and create ingress rule to
        expose your application to the internet.
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'

const props = defineProps({
  ingressRules: {
    type: Array,
    required: true
  },
  showAddLink: {
    type: Boolean,
    required: false,
    default: false
  },
  addLinkRoute: {
    type: String,
    required: false,
    default: ''
  }
})
</script>
