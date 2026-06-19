<script setup lang="ts">
import { ref } from 'vue'
import { AlertCircle, AlertTriangle, Info, XCircle, X } from '@lucide/vue'

export type AlertType = 'info' | 'warning' | 'error' | 'success'

interface Props {
  type?: AlertType
  title?: string
  message: string
  showClose?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  type: 'info',
  title: '',
  showClose: false,
})

const emit = defineEmits<{
  (e: 'close'): void
}>()

const isVisible = ref(true)

const iconMap = {
  info: Info,
  warning: AlertTriangle,
  error: XCircle,
  success: AlertCircle,
}

const colorMap = {
  info: 'bg-blue-50 border-blue-200 text-blue-800 dark:bg-blue-900/20 dark:border-blue-800 dark:text-blue-200',
  warning: 'bg-yellow-50 border-yellow-200 text-yellow-800 dark:bg-yellow-900/20 dark:border-yellow-800 dark:text-yellow-200',
  error: 'bg-red-50 border-red-200 text-red-800 dark:bg-red-900/20 dark:border-red-800 dark:text-red-200',
  success: 'bg-green-50 border-green-200 text-green-800 dark:bg-green-900/20 dark:border-green-800 dark:text-green-200',
}

function handleClose() {
  isVisible.value = false
  emit('close')
}

const Icon = iconMap[props.type]
</script>

<template>
  <div
    v-if="isVisible"
    class="flex items-start gap-3 p-4 rounded-lg border"
    :class="colorMap[type]"
  >
    <component
      :is="Icon"
      class="h-5 w-5 flex-shrink-0 mt-0.5"
    />
    <div class="flex-1 min-w-0">
      <p v-if="title" class="font-medium mb-1">{{ title }}</p>
      <p class="text-sm">{{ message }}</p>
    </div>
    <button
      v-if="showClose"
      class="flex-shrink-0 opacity-70 hover:opacity-100 transition-opacity"
      @click="handleClose"
    >
      <X class="h-4 w-4" />
    </button>
  </div>
</template>
