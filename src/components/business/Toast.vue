<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { CheckCircle, XCircle, AlertCircle, Info, X } from '@lucide/vue'

export type ToastType = 'success' | 'error' | 'warning' | 'info'

export interface Toast {
  id: string
  type: ToastType
  title: string
  message?: string
  duration?: number
}

// 全局状态
const toasts = ref<Toast[]>([])

// 图标映射
const iconMap = {
  success: CheckCircle,
  error: XCircle,
  warning: AlertCircle,
  info: Info,
}

// 颜色映射
const colorMap = {
  success: 'bg-green-50 border-green-200 text-green-800 dark:bg-green-900/20 dark:border-green-800 dark:text-green-200',
  error: 'bg-red-50 border-red-200 text-red-800 dark:bg-red-900/20 dark:border-red-800 dark:text-red-200',
  warning: 'bg-yellow-50 border-yellow-200 text-yellow-800 dark:bg-yellow-900/20 dark:border-yellow-800 dark:text-yellow-200',
  info: 'bg-blue-50 border-blue-200 text-blue-800 dark:bg-blue-900/20 dark:border-blue-800 dark:text-blue-200',
}

// 添加Toast
function addToast(toast: Omit<Toast, 'id'>) {
  const id = Date.now().toString() + Math.random().toString(36).substr(2, 9)
  const newToast: Toast = {
    ...toast,
    id,
  }

  toasts.value.push(newToast)

  // 自动移除
  const duration = toast.duration || 3000
  if (duration > 0) {
    setTimeout(() => {
      removeToast(id)
    }, duration)
  }

  return id
}

// 移除Toast
function removeToast(id: string) {
  const index = toasts.value.findIndex(t => t.id === id)
  if (index >= 0) {
    toasts.value.splice(index, 1)
  }
}

// 快捷方法
function success(title: string, message?: string, duration?: number) {
  return addToast({ type: 'success', title, message, duration })
}

function error(title: string, message?: string, duration?: number) {
  return addToast({ type: 'error', title, message, duration })
}

function warning(title: string, message?: string, duration?: number) {
  return addToast({ type: 'warning', title, message, duration })
}

function info(title: string, message?: string, duration?: number) {
  return addToast({ type: 'info', title, message, duration })
}

// 暴露方法
defineExpose({
  addToast,
  removeToast,
  success,
  error,
  warning,
  info,
})
</script>

<template>
  <Teleport to="body">
    <div class="fixed top-4 right-4 z-[300] space-y-2 max-w-md">
      <div
        v-for="toast in toasts"
        :key="toast.id"
        class="flex items-start gap-3 p-4 rounded-lg border shadow-lg animate-in slide-in-from-right"
        :class="colorMap[toast.type]"
      >
        <component
          :is="iconMap[toast.type]"
          class="h-5 w-5 flex-shrink-0 mt-0.5"
        />
        <div class="flex-1 min-w-0">
          <p class="font-medium">{{ toast.title }}</p>
          <p v-if="toast.message" class="text-sm mt-1 opacity-90">
            {{ toast.message }}
          </p>
        </div>
        <button
          class="flex-shrink-0 opacity-70 hover:opacity-100 transition-opacity"
          @click="removeToast(toast.id)"
        >
          <X class="h-4 w-4" />
        </button>
      </div>
    </div>
  </Teleport>
</template>
