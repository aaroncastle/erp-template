<script setup lang="ts">
import { FileText, Calendar, DollarSign, CheckCircle2, XCircle, Clock } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Card, CardContent } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'

export type QuotationStatus = 'pending' | 'confirmed' | 'expired'

interface Props {
  quotationNumber: string
  customerName: string
  totalAmount: number
  itemCount: number
  status: QuotationStatus
  validUntil: string
  createdAt: string
}

defineProps<Props>()

const emit = defineEmits<{
  (e: 'click'): void
  (e: 'edit'): void
  (e: 'delete'): void
}>()

function formatAmount(amount: number): string {
  return amount.toFixed(2)
}

function getStatusLabel(status: QuotationStatus): string {
  const labels = {
    pending: '待确认',
    confirmed: '已确认',
    expired: '已过期',
  }
  return labels[status]
}
</script>

<template>
  <Card class="hover:shadow-md transition-shadow cursor-pointer" @click="emit('click')">
    <!-- 标题区域（深色背景） -->
    <div class="bg-muted/50 px-4 py-3 border-b border-border">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <FileText class="h-4 w-4 text-primary" />
          <span class="text-sm font-medium text-foreground">报价单</span>
          <span class="text-xs text-muted-foreground">{{ quotationNumber }}</span>
        </div>
        <StatusBadge :status="status" />
      </div>
    </div>

    <!-- 内容区域（浅色背景） -->
    <CardContent class="p-4">
      <div class="space-y-3">
        <!-- 客户信息 -->
        <div class="flex items-center gap-2">
          <span class="text-sm text-muted-foreground">客户：</span>
          <span class="text-sm font-medium text-foreground">{{ customerName }}</span>
        </div>

        <!-- 项目数量和金额 -->
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-4">
            <div class="flex items-center gap-1.5">
              <FileText class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-foreground">{{ itemCount }} 项</span>
            </div>
            <div class="flex items-center gap-1.5">
              <DollarSign class="h-4 w-4 text-muted-foreground" />
              <span class="text-base font-semibold text-primary">¥{{ formatAmount(totalAmount) }}</span>
            </div>
          </div>
        </div>

        <!-- 日期信息 -->
        <div class="flex items-center gap-4 text-xs text-muted-foreground">
          <div class="flex items-center gap-1">
            <Calendar class="h-3 w-3" />
            <span>创建：{{ createdAt }}</span>
          </div>
          <div class="flex items-center gap-1">
            <Clock class="h-3 w-3" />
            <span>有效期至：{{ validUntil }}</span>
          </div>
        </div>
      </div>

      <!-- 操作按钮 -->
      <div class="flex items-center justify-end gap-2 mt-4 pt-4 border-t border-border">
        <Button variant="outline" size="sm" @click.stop="emit('edit')">
          编辑
        </Button>
        <Button variant="outline" size="sm" @click.stop="emit('delete')">
          删除
        </Button>
      </div>
    </CardContent>
  </Card>
</template>
