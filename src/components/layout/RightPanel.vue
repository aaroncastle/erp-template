<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, Plus, Bell, FileText, ShoppingCart } from '@lucide/vue'

type Module = 'quotation' | 'order'

interface Props {
  currentModule?: Module
  unreadNotificationCount?: number
}

const props = withDefaults(defineProps<Props>(), {
  currentModule: 'order',
  unreadNotificationCount: 0,
})

const emit = defineEmits<{
  (e: 'module-change', module: Module): void
  (e: 'tab-change', tab: string): void
  (e: 'search', query: string): void
  (e: 'create-new'): void
  (e: 'view-all-notifications'): void
  (e: 'notification-click', notification: any): void
}>()

const activeModule = ref<Module>(props.currentModule)
const activeTab = ref('all')
const searchQuery = ref('')

const modules = [
  { id: 'quotation' as const, label: '报价单', icon: FileText },
  { id: 'order' as const, label: '订单', icon: ShoppingCart },
]

const tabs = ['全部', '待处理', '进行中', '已完成']

const mockNotifications = [
  {
    id: '1',
    type: 'pending_invoice',
    title: '待开票通知',
    description: '订单 ORD-20260615-003',
    time: '10 分钟前',
  },
  {
    id: '2',
    type: 'modification_request',
    title: '修改申请',
    description: '订单 ORD-20260610-002',
    time: '1 小时前',
  },
  {
    id: '3',
    type: 'storage_complete',
    title: '入库完成',
    description: '订单 ORD-20260608-001',
    time: '2 小时前',
  },
]

function switchModule(module: Module) {
  activeModule.value = module
  emit('module-change', module)
}

function switchTab(tab: string) {
  activeTab.value = tab
  emit('tab-change', tab)
}

function handleSearch() {
  emit('search', searchQuery.value)
}

function handleNotificationClick(notification: any) {
  emit('notification-click', notification)
}
</script>

<template>
  <aside class="w-80 bg-background h-[calc(100vh-3.5rem-3rem)] rounded-lg overflow-hidden grid grid-rows-[auto_auto_auto_1fr] gap-4 p-4">
    <!-- 模块切换 + 新建 -->
    <div class="rounded-lg border border-border overflow-hidden">
      <div class="flex">
        <button
          v-for="module in modules"
          :key="module.id"
          class="flex-1 flex items-center justify-center gap-2 py-3 text-sm font-medium transition-colors relative"
          :class="activeModule === module.id ? 'text-foreground bg-accent' : 'text-muted-foreground hover:text-foreground'"
          @click="switchModule(module.id)"
        >
          <component :is="module.icon" class="h-4 w-4" />
          {{ module.label }}
          <div
            v-if="activeModule === module.id"
            class="absolute bottom-0 left-0 right-0 h-0.5 bg-primary"
          ></div>
        </button>
      </div>
      <div class="border-t border-border">
        <button
          class="w-full inline-flex items-center justify-center gap-2 px-3 py-2.5 text-sm font-medium text-primary hover:bg-accent transition-colors"
          @click="emit('create-new')"
        >
          <Plus class="h-4 w-4" />
          新建{{ activeModule === 'quotation' ? '报价单' : '订单' }}
        </button>
      </div>
    </div>

    <!-- Tab 状态筛选 -->
    <div class="rounded-lg border border-border p-2">
      <div class="flex gap-1">
        <button
          v-for="tab in tabs"
          :key="tab"
          class="flex-1 px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
          :class="activeTab === tab ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:bg-accent'"
          @click="switchTab(tab)"
        >
          {{ tab }}
        </button>
      </div>
    </div>

    <!-- 搜索框 -->
    <div class="rounded-lg border border-border p-3">
      <div class="relative">
        <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <input
          v-model="searchQuery"
          type="text"
          placeholder="搜索客户名称..."
          class="w-full rounded-md border border-input bg-background pl-9 pr-3 py-2 text-sm placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring"
          @input="handleSearch"
        />
      </div>
    </div>

    <!-- 通知列表 -->
    <div class="rounded-lg border border-border overflow-hidden flex flex-col">
      <div class="flex items-center justify-between p-3 border-b border-border">
        <h3 class="text-sm font-semibold text-foreground">
          通知
          <span v-if="unreadNotificationCount > 0" class="ml-1 text-xs text-muted-foreground">
            (未读 {{ unreadNotificationCount }})
          </span>
        </h3>
        <button
          class="text-xs text-primary hover:underline"
          @click="emit('view-all-notifications')"
        >
          查看全部 →
        </button>
      </div>

      <div class="flex-1 overflow-y-auto p-3 space-y-3">
        <div
          v-for="notification in mockNotifications"
          :key="notification.id"
          class="rounded-md border border-border p-3 cursor-pointer hover:bg-accent transition-colors"
          @click="handleNotificationClick(notification)"
        >
          <div class="flex items-start gap-3">
            <!-- 未读标记 -->
            <div class="mt-1.5 h-2 w-2 rounded-full bg-primary flex-shrink-0"></div>

            <!-- 内容 -->
            <div class="flex-1 min-w-0">
              <p class="text-sm font-medium text-foreground truncate">
                {{ notification.title }}
              </p>
              <p class="text-xs text-muted-foreground mt-1 truncate">
                {{ notification.description }}
              </p>
              <p class="text-xs text-muted-foreground mt-1">
                {{ notification.time }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </aside>
</template>

