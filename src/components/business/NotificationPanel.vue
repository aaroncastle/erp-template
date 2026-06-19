<script setup lang="ts">
import { ref, computed } from 'vue'
import { Bell } from '@lucide/vue'
import { Button } from '@/components/ui/button'

export interface Notification {
  id: string
  title: string
  description: string
  timestamp: string
  read: boolean
}

interface Props {
  notifications?: Notification[]
  unreadCount?: number
  title?: string
}

const props = withDefaults(defineProps<Props>(), {
  notifications: () => [
    {
      id: '1',
      title: '新订单通知',
      description: '您有一个新的订单待处理',
      timestamp: '10分钟前',
      read: false,
    },
    {
      id: '2',
      title: '审批提醒',
      description: '订单ORD-001需要您审批',
      timestamp: '1小时前',
      read: false,
    },
    {
      id: '3',
      title: '入库完成',
      description: '订单ORD-002已完成入库',
      timestamp: '2小时前',
      read: true,
    },
  ],
  unreadCount: 0,
  title: '通知',
})

const emit = defineEmits<{
  (e: 'click', notification: Notification): void
  (e: 'view-all'): void
}>()

const activeTab = ref<'unread' | 'all'>('unread')

const filteredNotifications = computed(() => {
  if (activeTab.value === 'unread') {
    return props.notifications.filter(n => !n.read)
  }
  return props.notifications
})

const unreadCount = computed(() => {
  return props.unreadCount || props.notifications.filter(n => !n.read).length
})

function handleClick(notification: Notification) {
  emit('click', notification)
}

function handleViewAll() {
  emit('view-all')
}
</script>

<template>
  <div class="flex flex-col h-full border border-border rounded-lg bg-background">
    <!-- 头部 -->
    <div class="px-4 py-3 border-b border-border">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <Bell class="h-4 w-4 text-foreground" />
          <h3 class="text-sm font-semibold text-foreground">{{ title }}</h3>
          <span
            v-if="unreadCount > 0"
            class="px-2 py-0.5 text-xs rounded-full bg-primary text-primary-foreground"
          >
            {{ unreadCount }}
          </span>
        </div>
      </div>

      <!-- Tab切换 -->
      <div class="flex gap-2 mt-3">
        <Button
          variant="ghost"
          size="sm"
          :class="activeTab === 'unread' ? 'bg-muted font-medium' : ''"
          @click="activeTab = 'unread'"
        >
          未读
        </Button>
        <Button
          variant="ghost"
          size="sm"
          :class="activeTab === 'all' ? 'bg-muted font-medium' : ''"
          @click="activeTab = 'all'"
        >
          全部
        </Button>
      </div>
    </div>

    <!-- 通知列表 -->
    <div class="flex-1 overflow-y-auto">
      <div v-if="filteredNotifications.length > 0" class="divide-y divide-border">
        <div
          v-for="notification in filteredNotifications"
          :key="notification.id"
          class="px-4 py-3 hover:bg-muted/50 cursor-pointer transition-colors"
          :class="{ 'bg-muted/30': !notification.read }"
          @click="handleClick(notification)"
        >
          <div class="flex items-start gap-2">
            <div
              v-if="!notification.read"
              class="h-2 w-2 rounded-full bg-primary mt-1.5 flex-shrink-0"
            />
            <div class="flex-1 min-w-0">
              <p class="text-sm font-medium text-foreground truncate">
                {{ notification.title }}
              </p>
              <p class="text-xs text-muted-foreground mt-1 truncate">
                {{ notification.description }}
              </p>
              <p class="text-xs text-muted-foreground mt-1">
                {{ notification.timestamp }}
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- 空状态 -->
      <div v-else class="flex flex-col items-center justify-center py-8 text-center">
        <Bell class="h-12 w-12 text-muted-foreground mb-2" />
        <p class="text-sm text-muted-foreground">
          {{ activeTab === 'unread' ? '暂无未读通知' : '暂无通知' }}
        </p>
      </div>
    </div>

    <!-- 底部 -->
    <div class="px-4 py-3 border-t border-border">
      <Button variant="ghost" size="sm" class="w-full" @click="handleViewAll">
        查看全部
      </Button>
    </div>
  </div>
</template>
