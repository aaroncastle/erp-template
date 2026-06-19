<script setup lang="ts">
import { computed } from 'vue'

export type StatusType =
  | 'draft'
  | 'pending'
  | 'in_progress'
  | 'completed'
  | 'archived'
  | 'pending_invoice'
  | 'pending_delivery'
  | 'pending_payment'
  | 'approved'
  | 'rejected'
  | 'cancelled'
  | 'confirmed'
  | 'expired'
  | 'issued'
  | 'partial'
  | 'overdue'

interface Props {
  status: StatusType
  size?: 'sm' | 'md'
}

const props = withDefaults(defineProps<Props>(), {
  size: 'sm',
})

const statusConfig = {
  draft: {
    label: '草稿',
    class: 'bg-muted text-muted-foreground',
  },
  pending: {
    label: '待处理',
    class: 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900/30 dark:text-yellow-300',
  },
  in_progress: {
    label: '进行中',
    class: 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-300',
  },
  completed: {
    label: '已完成',
    class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300',
  },
  archived: {
    label: '已归档',
    class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300',
  },
  pending_invoice: {
    label: '待开票',
    class: 'bg-purple-100 text-purple-800 dark:bg-purple-900/30 dark:text-purple-300',
  },
  pending_delivery: {
    label: '待发货',
    class: 'bg-orange-100 text-orange-800 dark:bg-orange-900/30 dark:text-orange-300',
  },
  pending_payment: {
    label: '待收款',
    class: 'bg-pink-100 text-pink-800 dark:bg-pink-900/30 dark:text-pink-300',
  },
  approved: {
    label: '已通过',
    class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300',
  },
  rejected: {
    label: '已驳回',
    class: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300',
  },
  cancelled: {
    label: '已取消',
    class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300',
  },
  confirmed: {
    label: '已确认',
    class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300',
  },
  expired: {
    label: '已过期',
    class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300',
  },
  issued: {
    label: '已开票',
    class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300',
  },
  partial: {
    label: '部分收款',
    class: 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-300',
  },
  overdue: {
    label: '已逾期',
    class: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300',
  },
}

const config = computed(() => statusConfig[props.status])
const sizeClass = computed(() => (props.size === 'sm' ? 'px-2 py-0.5 text-xs' : 'px-3 py-1 text-sm'))
</script>

<template>
  <span
    class="inline-flex items-center rounded-md font-medium"
    :class="[config.class, sizeClass]"
  >
    {{ config.label }}
  </span>
</template>
