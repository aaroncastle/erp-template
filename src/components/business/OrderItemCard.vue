<script setup lang="ts">
import { Edit, Trash2, FileText, Image } from '@lucide/vue'
import StatusBadge from './StatusBadge.vue'
import type { StatusType } from './StatusBadge.vue'

export interface OrderItem {
  id: string
  name: string
  specification?: string
  quantity: number
  unit?: string
  costPrice?: number
  sellPrice?: number
  materialCode?: string
  status?: StatusType
  isCustomerSpecified?: boolean
  customerNote?: string
  hasDualItem?: boolean
  dualItemName?: string
  dualItemNote?: string
}

interface Props {
  item: OrderItem
  showActions?: boolean
  showDualItem?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  showActions: true,
  showDualItem: true,
})

const emit = defineEmits<{
  (e: 'edit', item: OrderItem): void
  (e: 'delete', item: OrderItem): void
  (e: 'view-drawing', item: OrderItem): void
}>()

function formatPrice(price?: number): string {
  if (price === undefined) return '-'
  return `¥${price.toLocaleString('zh-CN', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`
}

function handleEdit() {
  emit('edit', props.item)
}

function handleDelete() {
  emit('delete', props.item)
}

function handleViewDrawing() {
  emit('view-drawing', props.item)
}
</script>

<template>
  <div class="border border-border rounded-lg overflow-hidden">
    <!-- 实际生产项（正常显示） -->
    <div
      class="p-3"
      :class="[item.isCustomerSpecified ? 'bg-muted/30' : 'bg-background']"
    >
      <div class="flex items-start justify-between mb-2">
        <div class="flex-1">
          <div class="flex items-center gap-2">
            <span v-if="item.isCustomerSpecified" class="text-xs text-muted-foreground">A项:</span>
            <span v-else class="text-xs text-muted-foreground">B项:</span>
            <span class="font-medium text-foreground">{{ item.name }}</span>
            <StatusBadge v-if="item.status" :status="item.status" size="sm" />
          </div>
          <p v-if="item.specification" class="text-xs text-muted-foreground mt-0.5">
            {{ item.specification }}
          </p>
        </div>
        <div v-if="item.sellPrice" class="text-right">
          <p class="text-sm font-medium text-foreground">{{ formatPrice(item.sellPrice) }}</p>
        </div>
      </div>

      <div class="flex items-center gap-4 text-xs text-muted-foreground">
        <span>数量: {{ item.quantity }}{{ item.unit || '' }}</span>
        <span v-if="item.costPrice">成本: {{ formatPrice(item.costPrice) }}</span>
        <span v-if="item.materialCode">物料码: {{ item.materialCode }}</span>
      </div>

      <!-- 操作按钮 -->
      <div v-if="showActions" class="flex items-center gap-2 mt-3 pt-3 border-t border-border/50">
        <button
          class="flex items-center gap-1 px-2 py-1 text-xs text-muted-foreground hover:text-foreground hover:bg-muted rounded transition-colors"
          @click="handleViewDrawing"
        >
          <Image class="h-3 w-3" />
          <span>查看图纸</span>
        </button>
        <button
          class="flex items-center gap-1 px-2 py-1 text-xs text-muted-foreground hover:text-foreground hover:bg-muted rounded transition-colors"
          @click="handleEdit"
        >
          <Edit class="h-3 w-3" />
          <span>编辑</span>
        </button>
        <button
          class="flex items-center gap-1 px-2 py-1 text-xs text-destructive hover:bg-destructive/10 rounded transition-colors"
          @click="handleDelete"
        >
          <Trash2 class="h-3 w-3" />
          <span>删除</span>
        </button>
      </div>
    </div>

    <!-- 客户指定项（灰度显示，如果存在阴阳项目） -->
    <div
      v-if="showDualItem && item.hasDualItem"
      class="p-3 bg-muted/50 border-t border-border/50"
    >
      <div class="flex items-center gap-2 mb-1">
        <span class="text-xs text-muted-foreground">A项 (客户指定):</span>
        <span class="text-sm text-muted-foreground">{{ item.dualItemName || item.name }}</span>
      </div>
      <p v-if="item.dualItemNote || item.customerNote" class="text-xs text-muted-foreground">
        备注: {{ item.dualItemNote || item.customerNote }}
      </p>
    </div>
  </div>
</template>
