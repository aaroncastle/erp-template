<script setup lang="ts">
import { ref, computed } from 'vue'
import { Edit, FileText, Truck, CheckCircle2 } from '@lucide/vue'
import Drawer from './Drawer.vue'
import DualItemCard from './DualItemCard.vue'
import StatusBadge from './StatusBadge.vue'
import ApprovalFlow from './ApprovalFlow.vue'

export type OrderDetailStatus = 'draft' | 'pending' | 'in_progress' | 'completed' | 'archived'

export interface OrderDetailItem {
  productionItem: {
    id: string
    number: string
    productName: string
    specification: string
    quantity: number
    unitPrice: number
    amount: number
    status: string
  }
  customerItems: {
    id: string
    number: string
    productName: string
    specification: string
    quantity: number
    unitPrice: number
    amount: number
    status: string
  }[]
}

export interface OrderDetail {
  id: string
  number: string
  customerName: string
  status: OrderDetailStatus
  createdAt: string
  updatedAt?: string
  itemCount: number
  totalAmount: number
  progress?: string
  deliveryStatus?: string
  invoiceStatus?: string
  items: OrderDetailItem[]
  approvalFlow?: {
    id: string
    title: string
    status: 'pending' | 'approved' | 'rejected' | 'cancelled'
    records: {
      id: string
      approver: string
      department: string
      status: 'pending' | 'approved' | 'rejected' | 'cancelled'
      comment?: string
      timestamp: string
    }[]
    currentStep: number
    totalSteps: number
    submitter?: string
    submitComment?: string
    businessData?: {
      type: 'order'
      orderNumber?: string
      customerName?: string
      totalAmount?: number
      modifications?: { field: string; before: string; after: string }[]
    }
  }
}

interface Props {
  order?: OrderDetail
  open?: boolean
  editable?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
  editable: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'edit', order: OrderDetail): void
  (e: 'create-invoice', order: OrderDetail): void
  (e: 'ship', order: OrderDetail): void
  (e: 'complete', order: OrderDetail): void
}>()

const showApprovalFlow = ref(false)

function handleClose() {
  showApprovalFlow.value = false
}

function handleEdit() {
  if (props.order) {
    emit('edit', props.order)
  }
}

function handleCreateInvoice() {
  if (props.order) {
    emit('create-invoice', props.order)
  }
}

function handleShip() {
  if (props.order) {
    emit('ship', props.order)
  }
}

function handleComplete() {
  if (props.order) {
    emit('complete', props.order)
  }
}

function handleAction(key: string) {
  switch (key) {
    case 'edit':
      handleEdit()
      break
    case 'invoice':
      handleCreateInvoice()
      break
    case 'ship':
      handleShip()
      break
    case 'complete':
      handleComplete()
      break
  }
}

function handleApprovalApprove(comment: string, ccList: any[]) {
  console.log('审批通过:', comment, '抄送人:', ccList)
  showApprovalFlow.value = false
}

function handleApprovalReject(comment: string, ccList: any[]) {
  console.log('审批驳回:', comment, '抄送人:', ccList)
  showApprovalFlow.value = false
}

// 计算状态对应的操作按钮
const availableActions = computed(() => {
  if (!props.order) return []

  const actions = []

  switch (props.order.status) {
    case 'draft':
      actions.push({ key: 'edit', label: '编辑', icon: Edit, variant: 'default' })
      break
    case 'pending':
      actions.push({ key: 'edit', label: '编辑', icon: Edit, variant: 'outline' })
      break
    case 'in_progress':
      if (!props.order.invoiceStatus || props.order.invoiceStatus === 'pending_invoice') {
        actions.push({ key: 'invoice', label: '申请开票', icon: FileText, variant: 'default' })
      }
      if (props.order.deliveryStatus !== 'shipped') {
        actions.push({ key: 'ship', label: '发货', icon: Truck, variant: 'default' })
      }
      break
    case 'completed':
      actions.push({ key: 'complete', label: '归档', icon: CheckCircle2, variant: 'outline' })
      break
  }

  return actions
})
</script>

<template>
  <Drawer
    :open="open"
    :title="order ? `订单 ${order.number}` : '订单详情'"
    @update:open="emit('update:open', $event)"
    @close="handleClose"
  >
    <!-- 左侧：订单项列表 -->
    <template #list>
      <div class="space-y-4">
        <!-- 订单项列表头部 -->
        <div class="flex items-center justify-between">
          <h3 class="text-sm font-semibold text-foreground">订单项列表</h3>
          <button
            v-if="editable"
            class="px-3 py-1.5 text-xs font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="handleEdit"
          >
            编辑
          </button>
        </div>

        <!-- 阴阳项目卡片列表 -->
        <div v-if="order?.items" class="space-y-3">
          <DualItemCard
            v-for="dualItem in order.items"
            :key="dualItem.productionItem.id"
            :item="dualItem.productionItem"
            :dual-items="dualItem.customerItems"
            :is-duality="dualItem.customerItems.length > 0"
          />
        </div>

        <!-- 空状态 -->
        <div v-else class="text-center py-8">
          <p class="text-muted-foreground">暂无订单项</p>
        </div>
      </div>
    </template>

    <!-- 右侧：订单详情 -->
    <template #detail>
      <div v-if="order" class="space-y-6">
        <!-- 订单状态 -->
        <div class="flex items-center justify-between">
          <h3 class="text-sm font-semibold text-foreground">订单状态</h3>
          <StatusBadge :status="order.status" size="md" />
        </div>

        <!-- 订单信息 -->
        <div class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">订单信息</h3>
          <div class="space-y-2 text-sm">
            <div class="flex items-center justify-between">
              <span class="text-muted-foreground">订单编号：</span>
              <span class="font-medium text-foreground">{{ order.number }}</span>
            </div>
            <div class="flex items-center justify-between">
              <span class="text-muted-foreground">客户名称：</span>
              <span class="font-medium text-foreground">{{ order.customerName }}</span>
            </div>
            <div class="flex items-center justify-between">
              <span class="text-muted-foreground">创建日期：</span>
              <span class="text-foreground">{{ order.createdAt }}</span>
            </div>
            <div v-if="order.updatedAt" class="flex items-center justify-between">
              <span class="text-muted-foreground">最后更新：</span>
              <span class="text-foreground">{{ order.updatedAt }}</span>
            </div>
            <div class="flex items-center justify-between">
              <span class="text-muted-foreground">项目数量：</span>
              <span class="text-foreground">{{ order.itemCount }} 项</span>
            </div>
            <div class="flex items-center justify-between pt-2 border-t border-border">
              <span class="text-muted-foreground font-medium">订单金额：</span>
              <span class="font-semibold text-primary text-base">¥{{ order.totalAmount.toFixed(2) }}</span>
            </div>
          </div>
        </div>

        <!-- 进度信息 -->
        <div v-if="order.progress || order.deliveryStatus || order.invoiceStatus" class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">进度信息</h3>
          <div class="space-y-2 text-sm">
            <div v-if="order.progress" class="flex items-center justify-between">
              <span class="text-muted-foreground">生产进度：</span>
              <span class="text-foreground">{{ order.progress }}</span>
            </div>
            <div v-if="order.deliveryStatus" class="flex items-center justify-between">
              <span class="text-muted-foreground">发货状态：</span>
              <StatusBadge :status="order.deliveryStatus as any" />
            </div>
            <div v-if="order.invoiceStatus" class="flex items-center justify-between">
              <span class="text-muted-foreground">开票状态：</span>
              <StatusBadge :status="order.invoiceStatus as any" />
            </div>
          </div>
        </div>

        <!-- 审批流程入口 -->
        <div v-if="order.approvalFlow" class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">审批流程</h3>
          <button
            class="w-full p-3 rounded-lg border border-border bg-muted/20 hover:bg-accent transition-colors text-left"
            @click="showApprovalFlow = true"
          >
            <div class="flex items-center justify-between">
              <div>
                <p class="text-sm font-medium text-foreground">{{ order.approvalFlow.title }}</p>
                <p class="text-xs text-muted-foreground mt-1">
                  步骤 {{ order.approvalFlow.currentStep }} / {{ order.approvalFlow.totalSteps }}
                </p>
              </div>
              <StatusBadge :status="order.approvalFlow.status" />
            </div>
          </button>
        </div>

        <!-- 操作记录 -->
        <div class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">操作记录</h3>
          <div class="space-y-2 text-sm text-muted-foreground">
            <p>• 2026-06-18 10:30 创建订单</p>
            <p>• 2026-06-18 14:20 开始生产</p>
            <p>• 2026-06-19 09:00 进度更新</p>
          </div>
        </div>
      </div>
    </template>

    <!-- 底部操作栏 -->
    <template #actions>
      <button
        v-for="action in availableActions"
        :key="action.key"
        class="inline-flex items-center gap-1.5 px-4 py-2 text-sm font-medium rounded-md transition-colors"
        :class="action.variant === 'default'
          ? 'bg-primary text-primary-foreground hover:bg-primary/90'
          : 'border border-input bg-background hover:bg-accent'"
        @click="handleAction(action.key)"
      >
        <component :is="action.icon" class="h-4 w-4" />
        {{ action.label }}
      </button>
      <button
        class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
        @click="emit('update:open', false)"
      >
        确定
      </button>
    </template>

    <!-- 审批流程对话框 -->
    <ApprovalFlow
      v-if="order?.approvalFlow"
      v-model:open="showApprovalFlow"
      :flow="order.approvalFlow"
      @approve="handleApprovalApprove"
      @reject="handleApprovalReject"
    />
  </Drawer>
</template>
