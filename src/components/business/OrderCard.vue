<script setup lang="ts">
import { Copy, Check, MoreHorizontal } from '@lucide/vue'
import { ref } from 'vue'

interface OrderItem {
  id: string
  name: string
  quantity: number
  price: number
}

interface Props {
  id: string
  number: string
  customerName: string
  status: string
  statusColor?: string
  createdAt: string
  itemCount: number
  totalAmount: number
  progress?: string
  deliveryStatus?: string
  items?: OrderItem[]
}

const props = withDefaults(defineProps<Props>(), {
  statusColor: 'default',
  progress: '',
  deliveryStatus: '',
  items: () => [],
})

const emit = defineEmits<{
  (e: 'click'): void
  (e: 'copy-number', number: string): void
  (e: 'more-menu'): void
}>()

const copied = ref(false)

const statusColors: Record<string, string> = {
  draft: 'bg-gray-500/10 text-gray-600',
  pending: 'bg-yellow-500/10 text-yellow-600',
  in_progress: 'bg-blue-500/10 text-blue-600',
  completed: 'bg-green-500/10 text-green-600',
  archived: 'bg-gray-500/10 text-gray-500',
}

const statusLabels: Record<string, string> = {
  draft: '草稿',
  pending: '待处理',
  in_progress: '进行中',
  completed: '已完成',
  archived: '已归档',
}

function handleCopyNumber() {
  navigator.clipboard.writeText(props.number)
  copied.value = true
  emit('copy-number', props.number)
  setTimeout(() => {
    copied.value = false
  }, 2000)
}
</script>

<template>
  <div
    class="rounded-lg border border-border bg-card cursor-pointer hover:shadow-md transition-shadow"
    @click="emit('click')"
  >
    <!-- 标题区域 -->
    <div class="px-4 py-3 border-b border-border bg-muted/30">
      <div class="flex items-center justify-between gap-2">
        <div class="flex items-center gap-2 flex-1 min-w-0">
          <span class="text-sm font-medium text-foreground truncate">
            订单 {{ number }}
          </span>
          <button
            class="p-1 rounded hover:bg-accent transition-colors"
            :title="'点击复制编号'"
            @click.stop="handleCopyNumber"
          >
            <Check v-if="copied" class="h-3.5 w-3.5 text-green-500" />
            <Copy v-else class="h-3.5 w-3.5 text-muted-foreground" />
          </button>
        </div>
        <div class="flex items-center gap-2">
          <span
            class="inline-flex items-center px-2 py-0.5 rounded text-xs font-medium"
            :class="statusColors[status] || statusColors.default"
          >
            {{ statusLabels[status] || status }}
          </span>
          <button
            class="p-1 rounded hover:bg-accent transition-colors"
            @click.stop="emit('more-menu')"
          >
            <MoreHorizontal class="h-4 w-4 text-muted-foreground" />
          </button>
        </div>
      </div>
    </div>

    <!-- 内容区域 -->
    <div class="px-4 py-3 space-y-2">
      <div class="flex items-center justify-between text-sm">
        <span class="text-muted-foreground">{{ customerName }}</span>
        <span class="text-muted-foreground">{{ createdAt }}</span>
      </div>

      <div v-if="progress" class="flex items-center justify-between text-sm">
        <span class="text-muted-foreground">入库进度</span>
        <span class="font-medium text-foreground">{{ progress }}</span>
      </div>

      <div v-if="deliveryStatus" class="flex items-center justify-between text-sm">
        <span class="text-muted-foreground">开票状态</span>
        <span class="font-medium text-foreground">{{ deliveryStatus }}</span>
      </div>

      <div class="flex items-center justify-between text-sm">
        <span class="text-muted-foreground">项目数</span>
        <span class="font-medium text-foreground">{{ itemCount }} 项</span>
      </div>

      <div class="flex items-center justify-between text-sm pt-1 border-t border-border">
        <span class="text-muted-foreground">总金额</span>
        <span class="font-semibold text-foreground">¥{{ totalAmount.toLocaleString('zh-CN', { minimumFractionDigits: 2 }) }}</span>
      </div>
    </div>

    <!-- 操作按钮区域 -->
    <div class="px-4 py-3 border-t border-border bg-muted/20 flex flex-wrap gap-2">
      <slot name="actions">
        <!-- 默认操作按钮 -->
      </slot>
    </div>
  </div>
</template>