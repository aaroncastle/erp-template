<script setup lang="ts">
import { Bell, ChevronRight } from '@lucide/vue'
import NotificationCard from './NotificationCard.vue'

export interface Notification {
  id: string
  type: 'pending_invoice' | 'modification_request' | 'storage_complete' | 'default'
  title: string
  description: string
  time: string
  unread?: boolean
  relatedId?: string
  relatedType?: string
}

interface Props {
  notifications: Notification[]
  title?: string
  showViewAll?: boolean
}

withDefaults(defineProps<Props>(), {
  title: '通知',
  showViewAll: true,
})

const emit = defineEmits<{
  (e: 'click', notification: Notification): void
  (e: 'view-all'): void
}>()

function handleClick(notification: Notification) {
  emit('click', notification)
}

function handleViewAll() {
  emit('view-all')
}
</script>

<template>
  <div class="flex flex-col h-full">
    <!-- 标题栏 -->
    <div class="flex items-center justify-between px-3 py-2 border-b border-border">
      <div class="flex items-center gap-2">
        <Bell class="h-4 w-4 text-muted-foreground" />
        <span class="text-sm font-medium text-foreground">{{ title }}</span>
        <span class="text-xs text-muted-foreground">({{ notifications.length }})</span>
      </div>
      <button
        v-if="showViewAll"
        class="flex items-center gap-1 text-xs text-primary hover:text-primary/80 transition-colors"
        @click="handleViewAll"
      >
        <span>查看全部</span>
        <ChevronRight class="h-3 w-3" />
      </button>
    </div>

    <!-- 通知列表 -->
    <div class="flex-1 overflow-y-auto">
      <div v-if="notifications.length === 0" class="flex flex-col items-center justify-center h-full p-4">
        <Bell class="h-8 w-8 text-muted-foreground/50 mb-2" />
        <p class="text-sm text-muted-foreground">暂无通知</p>
      </div>
      <div v-else class="p-2 space-y-2">
        <NotificationCard
          v-for="notification in notifications"
          :key="notification.id"
          :id="notification.id"
          :type="notification.type"
          :title="notification.title"
          :description="notification.description"
          :time="notification.time"
          :is-read="!notification.unread"
          @click="handleClick(notification)"
        />
      </div>
    </div>
  </div>
</template>
