<script setup lang="ts">
import { DollarSign, Calendar, CreditCard, FileText } from '@lucide/vue'
import { Card, CardContent } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'

export type PaymentStatus = 'pending' | 'partial' | 'completed' | 'overdue'

export type PaymentMethod = 'bank_transfer' | 'cash' | 'check' | 'wechat' | 'alipay'

export interface Payment {
  id: string
  paymentNumber: string
  orderNumber: string
  customerName: string
  amount: number
  paidAmount: number
  paymentDate: string
  dueDate: string
  status: PaymentStatus
  method: PaymentMethod
  remark?: string
}

interface Props {
  payment: Payment
}

defineProps<Props>()

const emit = defineEmits<{
  (e: 'click'): void
}>()

function formatAmount(amount: number): string {
  return amount.toFixed(2)
}

const methodLabels = {
  bank_transfer: '银行转账',
  cash: '现金',
  check: '支票',
  wechat: '微信支付',
  alipay: '支付宝',
}
</script>

<template>
  <Card class="hover:shadow-md transition-shadow cursor-pointer" @click="emit('click')">
    <!-- 标题区域 -->
    <div class="bg-muted/50 px-4 py-3 border-b border-border">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <DollarSign class="h-4 w-4 text-primary" />
          <span class="text-sm font-medium text-foreground">收款</span>
          <span class="text-xs text-muted-foreground">{{ payment.paymentNumber }}</span>
        </div>
        <StatusBadge :status="payment.status" />
      </div>
    </div>

    <!-- 内容区域 -->
    <CardContent class="p-4">
      <div class="space-y-3">
        <!-- 订单信息 -->
        <div class="flex items-center gap-2">
          <FileText class="h-4 w-4 text-muted-foreground" />
          <span class="text-sm text-muted-foreground">关联订单：</span>
          <span class="text-sm font-medium text-foreground">{{ payment.orderNumber }}</span>
        </div>

        <!-- 客户信息 -->
        <div class="flex items-center gap-2">
          <span class="text-sm text-muted-foreground">客户：</span>
          <span class="text-sm font-medium text-foreground">{{ payment.customerName }}</span>
        </div>

        <!-- 支付方式 -->
        <div class="flex items-center gap-2">
          <CreditCard class="h-4 w-4 text-muted-foreground" />
          <span class="text-sm text-muted-foreground">支付方式：</span>
          <span class="text-sm font-medium text-foreground">{{ methodLabels[payment.method] }}</span>
        </div>

        <!-- 金额信息 -->
        <div class="p-3 rounded-lg bg-muted/30">
          <div class="flex items-center justify-between mb-2">
            <span class="text-xs text-muted-foreground">应收金额</span>
            <span class="text-sm font-medium text-foreground">¥{{ formatAmount(payment.amount) }}</span>
          </div>
          <div class="flex items-center justify-between mb-2">
            <span class="text-xs text-muted-foreground">已收金额</span>
            <span class="text-sm font-medium text-green-600">¥{{ formatAmount(payment.paidAmount) }}</span>
          </div>
          <div class="flex items-center justify-between pt-2 border-t border-border">
            <span class="text-xs text-muted-foreground">待收金额</span>
            <span class="text-sm font-semibold text-primary">¥{{ formatAmount(payment.amount - payment.paidAmount) }}</span>
          </div>
        </div>

        <!-- 日期信息 -->
        <div class="flex items-center justify-between text-xs text-muted-foreground">
          <div class="flex items-center gap-1.5">
            <Calendar class="h-3 w-3" />
            <span>收款日期：{{ payment.paymentDate }}</span>
          </div>
          <div class="flex items-center gap-1.5">
            <Calendar class="h-3 w-3" />
            <span>到期日期：{{ payment.dueDate }}</span>
          </div>
        </div>
      </div>
    </CardContent>
  </Card>
</template>
