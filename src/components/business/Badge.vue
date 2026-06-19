<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  count?: number
  maxCount?: number
  dot?: boolean
  type?: 'primary' | 'success' | 'warning' | 'error' | 'info'
  showZero?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
  maxCount: 99,
  dot: false,
  type: 'error',
  showZero: false,
})

const displayCount = computed(() => {
  if (props.count > props.maxCount) {
    return `${props.maxCount}+`
  }
  return props.count.toString()
})

const shouldShow = computed(() => {
  if (props.dot) return true
  if (props.count > 0) return true
  if (props.showZero && props.count === 0) return true
  return false
})

const typeClasses = {
  primary: 'bg-primary text-primary-foreground',
  success: 'bg-green-500 text-white',
  warning: 'bg-yellow-500 text-white',
  error: 'bg-red-500 text-white',
  info: 'bg-blue-500 text-white',
}
</script>

<template>
  <div class="relative inline-flex">
    <slot />
    <span
      v-if="shouldShow"
      class="absolute -top-1 -right-1 flex items-center justify-center font-medium"
      :class="[
        typeClasses[type],
        dot ? 'h-2 w-2 rounded-full' : 'min-w-[20px] h-5 px-1.5 rounded-full text-xs',
      ]"
    >
      <span v-if="!dot">{{ displayCount }}</span>
    </span>
  </div>
</template>
