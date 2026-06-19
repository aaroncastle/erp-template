<script setup lang="ts">
import { ref, computed } from 'vue'
import { FileText, Calendar, DollarSign, User, Edit, Trash2, Send } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import Drawer from './Drawer.vue'
import StatusBadge from './StatusBadge.vue'

export type QuotationStatus = 'draft' | 'pending' | 'confirmed' | 'rejected' | 'expired'

export interface QuotationItem {
  id: string
  productName: string
  specification: string
  quantity: number
  unitPrice: number
  amount: number
}

export interface QuotationDetail {
  id: string
  number: string
  customerName: string
  contact?: string
  phone?: string
  status: QuotationStatus
  totalAmount: number
  validUntil: string
  createdAt: string
  updatedAt?: string
  items: QuotationItem[]
  remark?: string
  createdBy?: string
}

interface Props {
  quotation?: QuotationDetail
  open?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'edit', quotation: QuotationDetail): void
  (e: 'delete', quotation: QuotationDetail): void
  (e: 'submit', quotation: QuotationDetail): void
}>()

function handleEdit() {
  if (props.quotation) {
    emit('edit', props.quotation)
  }
}

function handleDelete() {
  if (props.quotation) {
    emit('delete', props.quotation)
  }
}

function handleSubmit() {
  if (props.quotation) {
    emit('submit', props.quotation)
  }
}

function formatAmount(amount: number): string {
  return amount.toFixed(2)
}

// 计算状态对应的操作按钮
const availableActions = computed(() => {
  if (!props.quotation) return []

  const actions = []

  switch (props.quotation.status) {
    case 'draft':
      actions.push({ key: 'edit', label: '编辑', icon: Edit, variant: 'default' })
      actions.push({ key: 'submit', label: '提交', icon: Send, variant: 'default' })
      actions.push({ key: 'delete', label: '删除', icon: Trash2, variant: 'destructive' })
      break
    case 'pending':
      actions.push({ key: 'edit', label: '编辑', icon: Edit, variant: 'outline' })
      break
    case 'confirmed':
      // 已确认的报价单不能编辑
      break
  }

  return actions
})

function handleAction(key: string) {
  switch (key) {
    case 'edit':
      handleEdit()
      break
    case 'delete':
      handleDelete()
      break
    case 'submit':
      handleSubmit()
      break
  }
}
</script>

<template>
  <Drawer
    :open="open"
    :title="quotation ? `报价单 ${quotation.number}` : '报价单详情'"
    @update:open="emit('update:open', $event)"
  >
    <!-- 左侧：报价项目列表 -->
    <template #list>
      <div v-if="quotation" class="space-y-4">
        <h3 class="text-sm font-semibold text-foreground">报价项目</h3>

        <!-- 项目列表 -->
        <div class="space-y-2">
          <div
            v-for="item in quotation.items"
            :key="item.id"
            class="p-3 border border-border rounded-lg bg-muted/30"
          >
            <div class="flex items-start justify-between">
              <div class="flex-1">
                <div class="flex items-center gap-2">
                  <FileText class="h-4 w-4 text-muted-foreground" />
                  <span class="text-sm font-medium text-foreground">{{ item.productName }}</span>
                </div>
                <p class="text-xs text-muted-foreground mt-1">{{ item.specification }}</p>
                <div class="flex items-center gap-4 mt-2 text-xs">
                  <span class="text-muted-foreground">数量：{{ item.quantity }}</span>
                  <span class="text-muted-foreground">单价：¥{{ formatAmount(item.unitPrice) }}</span>
                </div>
              </div>
              <div class="text-right">
                <p class="text-sm font-semibold text-primary">¥{{ formatAmount(item.amount) }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- 总金额 -->
        <div class="p-4 border border-border rounded-lg bg-primary/5">
          <div class="flex items-center justify-between">
            <span class="text-sm font-medium text-foreground">总金额</span>
            <span class="text-xl font-bold text-primary">¥{{ formatAmount(quotation.totalAmount) }}</span>
          </div>
        </div>
      </div>
    </template>

    <!-- 右侧：报价单详情 -->
    <template #detail>
      <div v-if="quotation" class="space-y-6">
        <!-- 状态 -->
        <div class="flex items-center justify-between">
          <h3 class="text-sm font-semibold text-foreground">状态</h3>
          <StatusBadge :status="quotation.status" />
        </div>

        <!-- 基本信息 -->
        <div class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">基本信息</h3>
          <div class="space-y-2">
            <div class="flex items-center gap-2">
              <FileText class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">报价单号：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.number }}</span>
            </div>
            <div class="flex items-center gap-2">
              <User class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">客户名称：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.customerName }}</span>
            </div>
            <div v-if="quotation.contact" class="flex items-center gap-2">
              <User class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">联系人：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.contact }}</span>
            </div>
            <div v-if="quotation.phone" class="flex items-center gap-2">
              <User class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">电话：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.phone }}</span>
            </div>
          </div>
        </div>

        <!-- 日期信息 -->
        <div class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">日期信息</h3>
          <div class="space-y-2">
            <div class="flex items-center gap-2">
              <Calendar class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">创建日期：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.createdAt }}</span>
            </div>
            <div v-if="quotation.updatedAt" class="flex items-center gap-2">
              <Calendar class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">更新日期：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.updatedAt }}</span>
            </div>
            <div class="flex items-center gap-2">
              <Calendar class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">有效期至：</span>
              <span class="text-sm font-medium text-foreground">{{ quotation.validUntil }}</span>
            </div>
          </div>
        </div>

        <!-- 创建人 -->
        <div v-if="quotation.createdBy" class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">创建人</h3>
          <div class="flex items-center gap-2">
            <User class="h-4 w-4 text-muted-foreground" />
            <span class="text-sm font-medium text-foreground">{{ quotation.createdBy }}</span>
          </div>
        </div>

        <!-- 备注 -->
        <div v-if="quotation.remark" class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">备注</h3>
          <p class="text-sm text-muted-foreground">{{ quotation.remark }}</p>
        </div>
      </div>
    </template>

    <!-- 底部操作栏 -->
    <template #actions>
      <Button
        v-for="action in availableActions"
        :key="action.key"
        :variant="action.variant as any"
        @click="handleAction(action.key)"
      >
        <component :is="action.icon" class="h-4 w-4 mr-1" />
        {{ action.label }}
      </Button>
      <Button variant="outline" @click="emit('update:open', false)">
        关闭
      </Button>
    </template>
  </Drawer>
</template>
