<template>
  <div class="flex w-full flex-col space-y-2 border-b bg-pri/5 px-4 pt-4">
    <div class="flex justify-between">
      <div class="flex flex-col">
        <p class="text-base font-medium">
          <slot name="title"></slot>
        </p>
        <p class="text-sm text-muted-foreground">
          <slot name="subtitle"></slot>
        </p>
      </div>
      <div class="flex flex-row gap-2">
        <slot name="buttons"></slot>
      </div>
    </div>
    <div class="flex gap-3 text-xs text-muted-foreground">
      <p
        v-for="tab in tabs"
        :key="tab.name"
        class="py-3"
        :class="{ active: currentTab === tab.name }"
        @click="currentTab = tab.name">
        {{ tab.name }}
      </p>
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
