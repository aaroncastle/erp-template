<script setup lang="ts">
import { ref } from 'vue'
import NotificationList from '@/components/business/NotificationList.vue'
import OrderItemCard from '@/components/business/OrderItemCard.vue'
import ApprovalTimeline from '@/components/business/ApprovalTimeline.vue'
import OperationLog from '@/components/business/OperationLog.vue'
import type { Notification } from '@/components/business/NotificationList.vue'
import type { OrderItem } from '@/components/business/OrderItemCard.vue'
import type { ApprovalNode } from '@/components/business/ApprovalTimeline.vue'
import type { OperationRecord } from '@/components/business/OperationLog.vue'

// 通知数据
const notifications = ref<Notification[]>([
  {
    id: '1',
    type: 'pending_invoice',
    title: '待开票通知',
    description: '订单 ORD-20260615-003',
    time: '10 分钟前',
    unread: true,
    relatedId: 'ORD-20260615-003',
  },
  {
    id: '2',
    type: 'modification_request',
    title: '修改申请',
    description: '订单 ORD-20260610-002',
    time: '1 小时前',
    unread: true,
  },
  {
    id: '3',
    type: 'storage_complete',
    title: '入库完成',
    description: '订单 ORD-20260608-001',
    time: '2 小时前',
    unread: true,
  },
])

// 订单项数据
const orderItems = ref<OrderItem[]>([
  {
    id: '1',
    name: '缠绕垫片 DN50 PN16 加强型',
    specification: 'DN50 PN16 加强型',
    quantity: 10,
    unit: '个',
    costPrice: 1200,
    sellPrice: 1500,
    materialCode: 'BCR-0001',
    status: 'in_progress',
    isCustomerSpecified: false,
    hasDualItem: true,
    dualItemName: '缠绕垫片 DN50 PN16',
    dualItemNote: '实际生产 B 项',
  },
  {
    id: '2',
    name: '金属垫片 DN100 PN25',
    specification: 'DN100 PN25',
    quantity: 5,
    unit: '个',
    costPrice: 850,
    sellPrice: 1100,
    materialCode: 'JH-0012',
    status: 'pending',
    hasDualItem: false,
  },
  {
    id: '3',
    name: '石墨环 DN80',
    specification: 'DN80',
    quantity: 20,
    unit: '个',
    costPrice: 400,
    sellPrice: 600,
    materialCode: 'SH-0003',
    status: 'completed',
    isCustomerSpecified: true,
    customerNote: '客户指定规格',
    hasDualItem: false,
  },
])

// 审批流程数据
const approvalNodes = ref<ApprovalNode[]>([
  {
    id: '1',
    title: '发起申请',
    approver: '张三',
    department: '销售一部',
    status: 'approved',
    time: '2026-06-18 14:30',
  },
  {
    id: '2',
    title: '综合办审核',
    approver: '李四',
    department: '综合办',
    status: 'rejected',
    time: '2026-06-18 15:00',
    comment: '车间排产已满，无法延期',
  },
  {
    id: '3',
    title: '多级审批',
    approver: '王五',
    status: 'pending',
    isCountersign: true,
    countersignApprovers: ['王五', '赵六'],
    ccList: ['孙七', '周八'],
  },
])

// 操作记录数据
const operationRecords = ref<OperationRecord[]>([
  {
    id: '1',
    action: '创建订单',
    operator: '张三',
    operatorDepartment: '销售一部',
    time: '06-15 10:00',
    details: '创建订单 ORD-20260615-003',
  },
  {
    id: '2',
    action: '转订单',
    operator: '张三',
    operatorDepartment: '销售一部',
    time: '06-16 09:30',
    details: '报价单转订单',
  },
  {
    id: '3',
    action: '入库',
    operator: '仓库管理员',
    operatorDepartment: '仓储部',
    time: '06-17 14:00',
    details: '入库 3 项',
  },
  {
    id: '4',
    action: '发货',
    operator: '仓库管理员',
    operatorDepartment: '仓储部',
    time: '06-18 16:00',
    details: '已发货',
  },
])

function handleNotificationClick(notification: Notification) {
  console.log('点击通知:', notification)
}

function handleViewAll() {
  console.log('查看全部通知')
}

function handleItemEdit(item: OrderItem) {
  console.log('编辑订单项:', item)
}

function handleItemDelete(item: OrderItem) {
  console.log('删除订单项:', item)
}

function handleViewDrawing(item: OrderItem) {
  console.log('查看图纸:', item)
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 7 - 业务展示增强组件</p>
      </div>

      <!-- NotificationList 通知列表 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">NotificationList - 通知列表</h2>
          <p class="text-sm text-muted-foreground mt-1">未读通知列表，支持查看全部入口</p>
        </div>

        <div class="p-4">
          <div class="h-[300px] border border-border rounded-lg overflow-hidden">
            <NotificationList
              :notifications="notifications"
              title="通知"
              :show-view-all="true"
              @click="handleNotificationClick"
              @view-all="handleViewAll"
            />
          </div>
        </div>
      </div>

      <!-- OrderItemCard 订单项卡片 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">OrderItemCard - 订单项卡片</h2>
          <p class="text-sm text-muted-foreground mt-1">订单项详情展示，支持阴阳项目</p>
        </div>

        <div class="p-4 space-y-3">
          <OrderItemCard
            v-for="item in orderItems"
            :key="item.id"
            :item="item"
            :show-actions="true"
            :show-dual-item="true"
            @edit="handleItemEdit"
            @delete="handleItemDelete"
            @view-drawing="handleViewDrawing"
          />
        </div>
      </div>

      <!-- ApprovalTimeline 审批时间线 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">ApprovalTimeline - 审批时间线</h2>
          <p class="text-sm text-muted-foreground mt-1">审批流程节点展示</p>
        </div>

        <div class="p-4">
          <div class="max-w-md">
            <ApprovalTimeline
              v-for="(node, index) in approvalNodes"
              :key="node.id"
              :node="node"
              :is-last="index === approvalNodes.length - 1"
            />
          </div>
        </div>
      </div>

      <!-- OperationLog 操作记录 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">OperationLog - 操作记录</h2>
          <p class="text-sm text-muted-foreground mt-1">操作记录列表展示</p>
        </div>

        <div class="p-4">
          <OperationLog
            :records="operationRecords"
            title="操作记录"
          />
        </div>
      </div>

      <!-- 功能特性说明 -->
      <div class="rounded-lg border border-border p-4">
        <h3 class="text-sm font-semibold text-foreground mb-2">Phase 7 组件特性</h3>
        <ul class="text-sm text-muted-foreground space-y-1">
          <li>• <strong>NotificationList</strong>: 通知列表，显示未读通知和查看全部入口</li>
          <li>• <strong>OrderItemCard</strong>: 订单项卡片，支持阴阳项目、操作按钮</li>
          <li>• <strong>ApprovalTimeline</strong>: 审批时间线，支持会签、抄送</li>
          <li>• <strong>OperationLog</strong>: 操作记录列表，显示操作人、时间、详情</li>
        </ul>
      </div>
    </div>
  </div>
</template>
