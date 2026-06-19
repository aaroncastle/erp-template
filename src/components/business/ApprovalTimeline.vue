<script setup lang="ts">
import { Check, X, Clock, User } from '@lucide/vue'

export interface ApprovalNode {
  id: string
  title: string
  approver?: string
  department?: string
  status: 'pending' | 'approved' | 'rejected' | 'waiting'
  time?: string
  comment?: string
  isCountersign?: boolean
  countersignApprovers?: string[]
  ccList?: string[]
}

interface Props {
  node: ApprovalNode
  isLast?: boolean
}

defineProps<Props>()

const statusConfig = {
  pending: {
    icon: Clock,
    color: 'text-yellow-500',
    bgColor: 'bg-yellow-100 dark:bg-yellow-900/30',
    label: '审批中',
  },
  approved: {
    icon: Check,
    color: 'text-green-500',
    bgColor: 'bg-green-100 dark:bg-green-900/30',
    label: '已通过',
  },
  rejected: {
    icon: X,
    color: 'text-destructive',
    bgColor: 'bg-destructive/10',
    label: '已驳回',
  },
  waiting: {
    icon: Clock,
    color: 'text-muted-foreground',
    bgColor: 'bg-muted',
    label: '待审批',
  },
}
</script>

<template>
  <div class="relative flex gap-3">
    <!-- 左侧时间线 -->
    <div class="flex flex-col items-center">
      <div
        class="flex items-center justify-center h-8 w-8 rounded-full"
        :class="[statusConfig[node.status].bgColor]"
      >
        <component
          :is="statusConfig[node.status].icon"
          class="h-4 w-4"
          :class="[statusConfig[node.status].color]"
        />
      </div>
      <!-- 连接线 -->
      <div
        v-if="!isLast"
        class="w-0.5 flex-1 min-h-[24px] bg-border"
      />
    </div>

    <!-- 右侧内容 -->
    <div class="flex-1 pb-4">
      <div class="flex items-center gap-2 mb-1">
        <span class="font-medium text-foreground">{{ node.title }}</span>
        <span
          class="text-xs px-2 py-0.5 rounded"
          :class="[statusConfig[node.status].bgColor, statusConfig[node.status].color]"
        >
          {{ statusConfig[node.status].label }}
        </span>
      </div>

      <!-- 审批人信息 -->
      <div v-if="node.approver" class="flex items-center gap-2 text-sm text-muted-foreground mb-1">
        <User class="h-3.5 w-3.5" />
        <span>{{ node.approver }}</span>
        <span v-if="node.department">- {{ node.department }}</span>
      </div>

      <!-- 时间 -->
      <p v-if="node.time" class="text-xs text-muted-foreground mb-1">
        {{ node.time }}
      </p>

      <!-- 备注/意见 -->
      <p v-if="node.comment" class="text-sm text-muted-foreground italic mb-1">
        {{ node.comment }}
      </p>

      <!-- 会签信息 -->
      <div v-if="node.isCountersign && node.countersignApprovers" class="mt-2 p-2 bg-muted rounded">
        <p class="text-xs text-muted-foreground mb-1">会签审批人:</p>
        <div class="flex flex-wrap gap-1">
          <span
            v-for="approver in node.countersignApprovers"
            :key="approver"
            class="text-xs px-2 py-0.5 bg-background rounded"
          >
            {{ approver }}
          </span>
        </div>
      </div>

      <!-- 抄送列表 -->
      <div v-if="node.ccList && node.ccList.length > 0" class="mt-2">
        <p class="text-xs text-muted-foreground">抄送: {{ node.ccList.join(', ') }}</p>
      </div>
    </div>
  </div>
</template>
