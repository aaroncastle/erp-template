<script setup lang="ts">
import { Package, Calendar, MapPin, FileText } from '@lucide/vue'
import { Card, CardContent } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'

export type WarehouseStatus = 'pending' | 'in_progress' | 'completed' | 'cancelled'

export interface WarehouseItem {
  id: string
  productName: string
  specification: string
  quantity: number
  unit: string
  location: string
}

export interface WarehouseRecord {
  id: string
  recordNumber: string
  orderNumber: string
  type: 'inbound' | 'outbound'
  status: WarehouseStatus
  operator: string
  operationDate: string
  items: WarehouseItem[]
  remark?: string
}

interface Props {
  record: WarehouseRecord
}

defineProps<Props>()

const emit = defineEmits<{
  (e: 'click'): void
}>()

const typeLabels = {
  inbound: '入库',
  outbound: '出库',
}
</script>

<template>
  <Card class="hover:shadow-md transition-shadow cursor-pointer" @click="emit('click')">
    <!-- 标题区域 -->
    <div class="bg-muted/50 px-4 py-3 border-b border-border">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <Package class="h-4 w-4 text-primary" />
          <span class="text-sm font-medium text-foreground">{{ typeLabels[record.type] }}</span>
          <span class="text-xs text-muted-foreground">{{ record.recordNumber }}</span>
        </div>
        <StatusBadge :status="record.status" />
      </div>
    </div>

    <!-- 内容区域 -->
    <CardContent class="p-4">
      <div class="space-y-3">
        <!-- 订单信息 -->
        <div class="flex items-center gap-2">
          <FileText class="h-4 w-4 text-muted-foreground" />
          <span class="text-sm text-muted-foreground">关联订单：</span>
          <span class="text-sm font-medium text-foreground">{{ record.orderNumber }}</span>
        </div>

        <!-- 操作人 -->
        <div class="flex items-center gap-2">
          <span class="text-sm text-muted-foreground">操作人：</span>
          <span class="text-sm font-medium text-foreground">{{ record.operator }}</span>
        </div>

        <!-- 物品列表 -->
        <div class="space-y-2">
          <div
            v-for="item in record.items"
            :key="item.id"
            class="flex items-center justify-between p-2 rounded-md bg-muted/30 text-xs"
          >
            <div class="flex-1">
              <div class="flex items-center gap-2">
                <Package class="h-3 w-3 text-muted-foreground" />
                <span class="font-medium text-foreground">{{ item.productName }}</span>
              </div>
              <div class="flex items-center gap-3 mt-1 text-muted-foreground">
                <span>规格：{{ item.specification }}</span>
                <span>数量：{{ item.quantity }} {{ item.unit }}</span>
              </div>
            </div>
            <div class="flex items-center gap-1 text-muted-foreground">
              <MapPin class="h-3 w-3" />
              <span>{{ item.location }}</span>
            </div>
          </div>
        </div>

        <!-- 日期信息 -->
        <div class="flex items-center gap-1.5 text-xs text-muted-foreground">
          <Calendar class="h-3 w-3" />
          <span>操作日期：{{ record.operationDate }}</span>
        </div>
      </div>
    </CardContent>
  </Card>
</template>
