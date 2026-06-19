<script setup lang="ts">
import { AlertTriangle } from '@lucide/vue'
import Modal from './Modal.vue'

interface Props {
  open: boolean
  title: string
  description: string
  confirmText?: string
  cancelText?: string
  type?: 'warning' | 'danger'
}

withDefaults(defineProps<Props>(), {
  confirmText: '确认',
  cancelText: '取消',
  type: 'warning',
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'confirm'): void
  (e: 'cancel'): void
}>()

function handleClose() {
  emit('update:open', false)
  emit('cancel')
}

function handleConfirm() {
  emit('confirm')
  emit('update:open', false)
}
</script>

<template>
  <Modal :open="open" @close="handleClose">
    <div class="p-6">
      <div class="flex items-start gap-4">
        <div
          class="flex items-center justify-center h-10 w-10 rounded-full"
          :class="[
            type === 'danger' ? 'bg-destructive/10' : 'bg-yellow-500/10'
          ]"
        >
          <AlertTriangle
            class="h-5 w-5"
            :class="[
              type === 'danger' ? 'text-destructive' : 'text-yellow-500'
            ]"
          />
        </div>
        <div class="flex-1">
          <h3 class="text-lg font-semibold text-foreground mb-2">{{ title }}</h3>
          <p class="text-sm text-muted-foreground">{{ description }}</p>
        </div>
      </div>
      <div class="flex justify-end gap-2 mt-6">
        <button
          class="px-4 py-2 text-sm font-medium text-foreground bg-muted rounded-md hover:bg-muted/80 transition-colors"
          @click="handleClose"
        >
          {{ cancelText }}
        </button>
        <button
          class="px-4 py-2 text-sm font-medium text-white rounded-md transition-colors"
          :class="[
            type === 'danger'
              ? 'bg-destructive hover:bg-destructive/90'
              : 'bg-yellow-500 hover:bg-yellow-600'
          ]"
          @click="handleConfirm"
        >
          {{ confirmText }}
        </button>
      </div>
    </div>
  </Modal>
</template>
