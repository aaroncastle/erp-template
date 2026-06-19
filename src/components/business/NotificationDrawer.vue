<script setup lang="ts">
import { ref, computed } from 'vue'
import { Bell, FileText, ShoppingCart, CheckCheck } from '@lucide/vue'
import Drawer from './Drawer.vue'
import StatusBadge from './StatusBadge.vue'

export type NotificationType = 'pending_invoice' | 'modification_request' | 'storage_complete' | 'approval_pending' | 'order_update'

export type NotificationStatus = 'unread' | 'read'

export interface Notification {
  id: string
  type: NotificationType
  title: string
  description: string
  timestamp: string
  status: NotificationStatus
  businessId?: string // 关联的业务 ID（订单号、审批单号等）
  businessType?: 'order' | 'quotation' | 'invoice' | 'approval'
}

interface Props {
  notifications?: Notification[]
  open?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'close'): void
  (e: 'notification-click', notification: Notification): void
  (e: 'mark-as-read', notificationId: string): void
  (e: 'mark-all-as-read'): void
}>()

const activeTab = ref<'all' | 'unread'>('unread')

const filteredNotifications = computed(() => {
  if (!props.notifications) return []

  if (activeTab.value === 'unread') {
    return props.notifications.filter(n => n.status === 'unread')
  }

  return props.notifications
})

const unreadCount = computed(() => {
  if (!props.notifications) return 0
  return props.notifications.filter(n => n.status === 'unread').length
})

const typeConfig = {
  pending_invoice: {
    label: '待开票通知',
    icon: FileText,
    color: 'text-purple-600 dark:text-purple-400',
  },
  modification_request: {
    label: '修改申请',
    icon: FileText,
    color: 'text-orange-600 dark:text-orange-400',
  },
  storage_complete: {
    label: '入库完成',
    icon: ShoppingCart,
    color: 'text-green-600 dark:text-green-400',
  },
  approval_pending: {
    label: '待审批',
    icon: Bell,
    color: 'text-blue-600 dark:text-blue-400',
  },
  order_update: {
    label: '订单更新',
    icon: ShoppingCart,
    color: 'text-gray-600 dark:text-gray-400',
  },
}

function handleNotificationClick(notification: Notification) {
  // 标记为已读
  if (notification.status === 'unread') {
    emit('mark-as-read', notification.id)
  }
  emit('notification-click', notification)
}

function handleMarkAllAsRead() {
  emit('mark-all-as-read')
}

function handleClose() {
  activeTab.value = 'unread'
}
</script>

<template>
  <Drawer
    :open="open"
    title="通知中心"
    @update:open="emit('update:open', $event)"
    @close="handleClose"
  >
    <!-- 左侧：通知列表 -->
    <template #list>
      <div class="space-y-4">
        <!-- 头部：Tab 切换 + 全部已读 -->
        <div class="flex items-center justify-between">
          <div class="inline-flex rounded-md border border-border p-1">
            <button
              class="px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
              :class="activeTab === 'unread' ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:text-foreground'"
              @click="activeTab = 'unread'"
            >
              未读 ({{ unreadCount }})
            </button>
            <button
              class="px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
              :class="activeTab === 'all' ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:text-foreground'"
              @click="activeTab = 'all'"
            >
              全部
            </button>
          </div>

          <button
            v-if="unreadCount > 0"
            class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
            @click="handleMarkAllAsRead"
          >
            <CheckCheck class="h-3.5 w-3.5" />
            全部已读
          </button>
        </div>

        <!-- 通知列表 -->
        <div v-if="filteredNotifications.length > 0" class="space-y-2">
          <div
            v-for="notification in filteredNotifications"
            :key="notification.id"
            class="rounded-lg border border-border p-3 cursor-pointer hover:bg-accent transition-colors"
            :class="notification.status === 'unread' ? 'bg-muted/20' : ''"
            @click="handleNotificationClick(notification)"
          >
            <div class="flex items-start gap-3">
              <!-- 未读标记 -->
              <div v-if="notification.status === 'unread'" class="mt-1.5 h-2 w-2 rounded-full bg-primary flex-shrink-0"></div>
              <div v-else class="mt-1.5 w-2 flex-shrink-0"></div>

              <!-- 类型图标 -->
              <div class="flex-shrink-0">
                <component
                  :is="typeConfig[notification.type].icon"
                  class="h-5 w-5"
                  :class="typeConfig[notification.type].color"
                />
              </div>

              <!-- 内容 -->
              <div class="flex-1 min-w-0">
                <div class="flex items-start justify-between gap-2">
                  <p class="text-sm font-medium text-foreground truncate">
                    {{ notification.title }}
                  </p>
                  <span
                    class="inline-flex items-center rounded-md px-2 py-0.5 text-xs font-medium"
                    :class="typeConfig[notification.type].color.replace('text-', 'bg-').replace('600', '100').replace('400', '900/30') + ' ' + typeConfig[notification.type].color"
                  >
                    {{ typeConfig[notification.type].label }}
                  </span>
                </div>
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
        <div v-else class="text-center py-8">
          <Bell class="h-12 w-12 mx-auto text-muted-foreground mb-2" />
          <p class="text-muted-foreground">
            {{ activeTab === 'unread' ? '暂无未读通知' : '暂无通知' }}
          </p>
        </div>
      </div>
    </template>

    <!-- 右侧：通知详情 -->
    <template #detail>
      <div class="space-y-4">
        <h3 class="text-sm font-semibold text-foreground">通知详情</h3>
        <p class="text-sm text-muted-foreground">
          点击左侧通知查看详情
        </p>
      </div>
    </template>

    <!-- 底部操作栏 -->
    <template #actions>
      <button
        class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
        @click="emit('update:open', false)"
      >
        关闭
      </button>
    </template>
  </Drawer>
</template>
