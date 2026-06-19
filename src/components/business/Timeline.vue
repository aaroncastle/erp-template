<script setup lang="ts">
import { CheckCircle, Circle, Clock, XCircle } from '@lucide/vue'

export type TimelineStatus = 'pending' | 'in_progress' | 'completed' | 'error'

export interface TimelineItem {
  id: string
  title: string
  description?: string
  timestamp?: string
  status?: TimelineStatus
  icon?: any
}

interface Props {
  items: TimelineItem[]
  direction?: 'vertical' | 'horizontal'
}

const props = withDefaults(defineProps<Props>(), {
  direction: 'vertical',
})

const statusConfig = {
  pending: {
    icon: Circle,
    class: 'text-muted-foreground border-muted-foreground',
  },
  in_progress: {
    icon: Clock,
    class: 'text-blue-600 border-blue-600 dark:text-blue-400 dark:border-blue-400',
  },
  completed: {
    icon: CheckCircle,
    class: 'text-green-600 border-green-600 dark:text-green-400 dark:border-green-400',
  },
  error: {
    icon: XCircle,
    class: 'text-red-600 border-red-600 dark:text-red-400 dark:border-red-400',
  },
}
</script>

<template>
  <div
    class="space-y-0"
    :class="direction === 'horizontal' ? 'flex gap-4' : ''"
  >
    <div
      v-for="(item, index) in items"
      :key="item.id"
      class="relative"
      :class="direction === 'horizontal' ? 'flex-1' : ''"
    >
      <!-- 垂直布局 -->
      <div v-if="direction === 'vertical'" class="flex gap-4 pb-6">
        <!-- 图标和连接线 -->
        <div class="flex flex-col items-center">
          <div
            class="flex items-center justify-center h-8 w-8 rounded-full border-2 bg-background"
            :class="statusConfig[item.status || 'pending'].class"
          >
            <component
              :is="item.icon || statusConfig[item.status || 'pending'].icon"
              class="h-4 w-4"
            />
          </div>
          <!-- 连接线 -->
          <div
            v-if="index < items.length - 1"
            class="flex-1 w-0.5 bg-border mt-2"
          />
        </div>

        <!-- 内容 -->
        <div class="flex-1 pt-1">
          <div class="flex items-center justify-between">
            <h4 class="text-sm font-medium text-foreground">{{ item.title }}</h4>
            <span v-if="item.timestamp" class="text-xs text-muted-foreground">
              {{ item.timestamp }}
            </span>
          </div>
          <p v-if="item.description" class="text-sm text-muted-foreground mt-1">
            {{ item.description }}
          </p>
        </div>
      </div>

      <!-- 水平布局 -->
      <div v-else class="flex flex-col items-center text-center">
        <!-- 图标 -->
        <div
          class="flex items-center justify-center h-10 w-10 rounded-full border-2 bg-background mb-2"
          :class="statusConfig[item.status || 'pending'].class"
        >
          <component
            :is="item.icon || statusConfig[item.status || 'pending'].icon"
            class="h-5 w-5"
          />
        </div>

        <!-- 内容 -->
        <h4 class="text-sm font-medium text-foreground">{{ item.title }}</h4>
        <p v-if="item.description" class="text-xs text-muted-foreground mt-1">
          {{ item.description }}
        </p>
        <span v-if="item.timestamp" class="text-xs text-muted-foreground mt-1">
          {{ item.timestamp }}
        </span>

        <!-- 连接线 -->
        <div
          v-if="index < items.length - 1"
          class="absolute top-5 left-full w-full h-0.5 bg-border -translate-x-1/2"
        />
      </div>
    </div>
  </div>
</template>
