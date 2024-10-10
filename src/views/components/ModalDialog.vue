<script setup>
import { Dialog, DialogDescription, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from '@headlessui/vue'
import FilledButton from './FilledButton.vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false
  },
  closeModal: {
    type: Function,
    default: () => {}
  },
  nonCancelable: {
    type: Boolean,
    default: false
  },
  width: {
    type: String,
    default: 'md',
    validator: (value) => {
      return ['sm', 'md', 'lg', 'xl', '2xl', '4xl', '6xl'].includes(value)
    }
  }
})

const closeModalWithValidation = () => {
  if (props.nonCancelable) {
    return
  }
  props.closeModal()
}
</script>

<template>
  <TransitionRoot :show="isOpen" appear as="template">
    <Dialog as="div" class="relative z-10 select-none" @close="closeModalWithValidation">
      <TransitionChild
        as="template"
        enter="duration-300 ease-out"
        enter-from="opacity-0"
        enter-to="opacity-100"
        leave="duration-200 ease-in"
        leave-from="opacity-100"
        leave-to="opacity-0">
        <div class="fixed inset-0 bg-black/50" />
      </TransitionChild>
      <div class="fixed inset-0 overflow-y-auto">
        <div class="flex min-h-full items-center justify-center p-4 text-center">
          <TransitionChild
            as="template"
            enter="duration-300 ease-out"
            enter-from="opacity-0 scale-95"
            enter-to="opacity-100 scale-100"
            leave="duration-200 ease-in"
            leave-from="opacity-100 scale-100"
            leave-to="opacity-0 scale-95">
            <DialogPanel
              :class="{
                'max-w-sm': width === 'sm',
                'max-w-md': width === 'md',
                'max-w-lg': width === 'lg',
                'max-w-xl': width === 'xl',
                'max-w-2xl': width === '2xl',
                'max-w-4xl': width === '4xl',
                'max-w-6xl': width === '6xl'
              }"
              class="w-full transform overflow-hidden rounded-lg border bg-background p-4 px-0 text-left align-middle shadow-xl transition-all">
              <DialogTitle as="h3" class="border-b px-4 pb-3 text-lg font-semibold leading-6">
                <slot name="header"></slot>
                <!-- Close button -->
                <FilledButton
                  v-show="!nonCancelable"
                  class="absolute right-2 top-2"
                  slim
                  type="outline"
                  @click="closeModalWithValidation">
                  <span class="sr-only">Close</span>
                  <Icon icon="lucide:x" class="h-5 w-5" />
                </FilledButton>
              </DialogTitle>

              <DialogDescription class="mt-2 px-4 text-sm text-muted-foreground">
                <slot name="body"></slot>
              </DialogDescription>

              <div class="mt-4 flex flex-row justify-end gap-2 px-4">
                <slot name="footer"></slot>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>
