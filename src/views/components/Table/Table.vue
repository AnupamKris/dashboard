<script setup>
defineProps({
  dividerBetweenRows: {
    type: Boolean,
    required: false,
    default: true
  }
})
</script>
<template>
  <div class="scrollbox max-h-[80vh] overflow-y-auto border md:rounded-lg">
    <table class="min-w-full divide-y divide-border">
      <thead class="bg-background">
        <tr>
          <slot name="header"></slot>
        </tr>
      </thead>
      <tbody
        class="bg-background"
        :class="{
          'divide-y-0': dividerBetweenRows,
          'divide-y divide-border ': dividerBetweenRows
        }">
        <slot name="body"></slot>
      </tbody>
    </table>
    <slot name="message"></slot>
  </div>
</template>
