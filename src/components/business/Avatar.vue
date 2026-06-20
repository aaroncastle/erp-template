<script setup lang="ts">
import { computed } from 'vue'
import { User } from '@lucide/vue'

interface Props {
  src?: string
  alt?: string
  name?: string
  size?: 'sm' | 'md' | 'lg' | 'xl'
  shape?: 'circle' | 'square'
}

const props = withDefaults(defineProps<Props>(), {
  alt: '',
  name: '',
  size: 'md',
  shape: 'circle',
})

const sizeClasses = {
  sm: 'h-8 w-8 text-xs',
  md: 'h-10 w-10 text-sm',
  lg: 'h-12 w-12 text-base',
  xl: 'h-16 w-16 text-lg',
}

const shapeClasses = {
  circle: 'rounded-full',
  square: 'rounded-md',
}

// 生成首字母
const initial = computed(() => {
  if (props.name) {
    return props.name.charAt(0).toUpperCase()
  }
  return ''
})

// 生成背景色（基于名字）
const bgColor = computed(() => {
  if (!props.name) return 'bg-primary/10 text-primary'

  const colors = [
    'bg-blue-100 text-blue-600 dark:bg-blue-900/30 dark:text-blue-400',
    'bg-green-100 text-green-600 dark:bg-green-900/30 dark:text-green-400',
    'bg-purple-100 text-purple-600 dark:bg-purple-900/30 dark:text-purple-400',
    'bg-orange-100 text-orange-600 dark:bg-orange-900/30 dark:text-orange-400',
    'bg-pink-100 text-pink-600 dark:bg-pink-900/30 dark:text-pink-400',
    'bg-teal-100 text-teal-600 dark:bg-teal-900/30 dark:text-teal-400',
  ]

  const index = props.name.charCodeAt(0) % colors.length
  return colors[index]
})
</script>

<template>
  <div
    class="flex items-center justify-center overflow-hidden"
    :class="[sizeClasses[size], shapeClasses[shape]]"
  >
    <!-- 图片 -->
    <img
      v-if="src"
      :src="src"
      :alt="alt || name"
      class="h-full w-full object-cover"
    />
    <!-- 首字母 -->
    <div
      v-else-if="initial"
      class="h-full w-full flex items-center justify-center font-medium"
      :class="bgColor"
    >
      {{ initial }}
    </div>
    <!-- 默认图标 -->
    <div
      v-else
      class="h-full w-full flex items-center justify-center bg-primary/10 text-primary"
    >
      <User class="h-1/2 w-1/2" />
    </div>
  </div>
</template>
