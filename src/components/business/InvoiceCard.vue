<script setup lang="ts">
import { FileText, Calendar, DollarSign, Download, Printer } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Card, CardContent } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'

export type InvoiceStatus = 'pending' | 'approved' | 'issued' | 'rejected'

export interface Invoice {
  id: string
  invoiceNumber: string
  orderNumber: string
  customerName: string
  amount: number
  taxAmount: number
  totalAmount: number
  issueDate: string
  status: InvoiceStatus
  type: 'vat_normal' | 'vat_special'
}

interface Props {
  invoice: Invoice
}

defineProps<Props>()

const emit = defineEmits<{
  (e: 'click'): void
  (e: 'download'): void
  (e: 'print'): void
}>()

function formatAmount(amount: number): string {
  return amount.toFixed(2)
}

const typeLabels = {
  vat_normal: '增值税普通发票',
  vat_special: '增值税专用发票',
}
</script>

<template>
  <Card class="hover:shadow-md transition-shadow cursor-pointer" @click="emit('click')">
    <!-- 标题区域 -->
    <div class="bg-muted/50 px-4 py-3 border-b border-border">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <FileText class="h-4 w-4 text-primary" />
          <span class="text-sm font-medium text-foreground">发票</span>
          <span class="text-xs text-muted-foreground">{{ invoice.invoiceNumber }}</span>
        </div>
        <StatusBadge :status="invoice.status" />
      </div>
    </div>

    <!-- 内容区域 -->
    <CardContent class="p-4">
      <div class="space-y-3">
        <!-- 订单信息 -->
        <div class="flex items-center gap-2">
          <span class="text-sm text-muted-foreground">关联订单：</span>
          <span class="text-sm font-medium text-foreground">{{ invoice.orderNumber }}</span>
        </div>

        <!-- 客户信息 -->
        <div class="flex items-center gap-2">
          <span class="text-sm text-muted-foreground">客户：</span>
          <span class="text-sm font-medium text-foreground">{{ invoice.customerName }}</span>
        </div>

        <!-- 发票类型 -->
        <div class="flex items-center gap-2">
          <span class="text-sm text-muted-foreground">类型：</span>
          <span class="text-sm font-medium text-foreground">{{ typeLabels[invoice.type] }}</span>
        </div>

        <!-- 金额信息 -->
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-4">
            <div class="flex items-center gap-1.5">
              <DollarSign class="h-4 w-4 text-muted-foreground" />
              <span class="text-xs text-muted-foreground">金额</span>
            </div>
            <div class="flex items-center gap-2 text-xs text-muted-foreground">
              <span>¥{{ formatAmount(invoice.amount) }}</span>
              <span>+</span>
              <span>税 ¥{{ formatAmount(invoice.taxAmount) }}</span>
            </div>
          </div>
          <div class="text-right">
            <p class="text-base font-semibold text-primary">¥{{ formatAmount(invoice.totalAmount) }}</p>
          </div>
        </div>

        <!-- 日期信息 -->
        <div class="flex items-center gap-1.5 text-xs text-muted-foreground">
          <Calendar class="h-3 w-3" />
          <span>开票日期：{{ invoice.issueDate }}</span>
        </div>
      </div>

      <!-- 操作按钮 -->
      <div class="flex items-center justify-end gap-2 mt-4 pt-4 border-t border-border">
        <Button variant="outline" size="sm" @click.stop="emit('download')">
          <Download class="h-4 w-4 mr-1" />
          下载
        </Button>
        <Button variant="outline" size="sm" @click.stop="emit('print')">
          <Printer class="h-4 w-4 mr-1" />
          打印
        </Button>
      </div>
    </CardContent>
  </Card>
</template>
