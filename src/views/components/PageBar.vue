<template>
  <div class="flex w-full flex-col gap-3 space-y-2 border-b bg-background px-4 pt-4 dark:bg-pri/5">
    <div class="flex justify-between">
      <div class="flex flex-col">
        <div class="text-base font-medium">
          <slot name="title"></slot>
        </div>
        <div class="text-sm text-muted-foreground">
          <slot name="subtitle"></slot>
        </div>
      </div>
      <div class="flex flex-row gap-2">
        <slot name="buttons"></slot>
      </div>
    </div>
    <div class="flex gap-3 text-sm text-muted-foreground">
      <div
        v-for="tab in tabs"
        :key="tab.name"
        class="flex cursor-pointer items-center gap-2 py-3"
        :class="{ active: currentTab === tab.path }"
        @click="currentTab = tab.path">
        <p>{{ tab.name }}</p>
        <p
          class="flex h-4 w-4 items-center justify-center rounded-full bg-pri text-xs text-background"
          v-if="tab.count">
          {{ tab.count }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  tabs: {
    type: Array,
    required: true
  }
})
const currentTab = defineModel()
</script>

<style scoped>
.active {
  @apply border-b-2 border-pri text-foreground;
}
</style>
