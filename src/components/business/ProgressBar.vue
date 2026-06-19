<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  value: number
  max?: number
  size?: 'sm' | 'md' | 'lg'
  showLabel?: boolean
  color?: 'primary' | 'success' | 'warning' | 'error'
}

const props = withDefaults(defineProps<Props>(), {
  max: 100,
  size: 'md',
  showLabel: false,
  color: 'primary',
})

const percentage = computed(() => {
  return Math.min(Math.max((props.value / props.max) * 100, 0), 100)
})

const sizeClasses = {
  sm: 'h-1',
  md: 'h-2',
  lg: 'h-3',
}

const colorClasses = {
  primary: 'bg-primary',
  success: 'bg-green-500',
  warning: 'bg-yellow-500',
  error: 'bg-red-500',
}
</script>

<template>
  <div class="space-y-1">
    <div
      class="w-full rounded-full bg-muted overflow-hidden"
      :class="sizeClasses[size]"
    >
      <div
        class="h-full rounded-full transition-all duration-300"
        :class="colorClasses[color]"
        :style="{ width: `${percentage}%` }"
      />
    </div>
    <div v-if="showLabel" class="flex items-center justify-between text-xs text-muted-foreground">
      <span>{{ value }} / {{ max }}</span>
      <span>{{ percentage.toFixed(0) }}%</span>
    </div>
  </div>
</template>
