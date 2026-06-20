<script setup lang="ts">
import { History, User } from '@lucide/vue'

export interface OperationRecord {
  id: string
  action: string
  operator: string
  operatorDepartment?: string
  time: string
  details?: string
}

interface Props {
  records: OperationRecord[]
  title?: string
  showEmpty?: boolean
}

withDefaults(defineProps<Props>(), {
  title: '操作记录',
  showEmpty: true,
})
</script>

<template>
  <div class="border border-border rounded-lg overflow-hidden">
    <!-- 标题 -->
    <div class="flex items-center gap-2 px-3 py-2 bg-muted border-b border-border">
      <History class="h-4 w-4 text-muted-foreground" />
      <span class="text-sm font-medium text-foreground">{{ title }}</span>
    </div>

    <!-- 记录列表 -->
    <div class="p-3">
      <div v-if="records.length === 0 && showEmpty" class="flex flex-col items-center justify-center py-6">
        <History class="h-8 w-8 text-muted-foreground/50 mb-2" />
        <p class="text-sm text-muted-foreground">暂无操作记录</p>
      </div>

      <div v-else class="space-y-3">
        <div
          v-for="record in records"
          :key="record.id"
          class="flex gap-3 text-sm"
        >
          <!-- 时间 -->
          <span class="text-xs text-muted-foreground whitespace-nowrap min-w-[80px]">
            {{ record.time }}
          </span>

          <!-- 操作内容 -->
          <div class="flex-1">
            <div class="flex items-center gap-2">
              <span class="font-medium text-foreground">{{ record.action }}</span>
              <div class="flex items-center gap-1 text-xs text-muted-foreground">
                <User class="h-3 w-3" />
                <span>{{ record.operator }}</span>
                <span v-if="record.operatorDepartment">({{ record.operatorDepartment }})</span>
              </div>
            </div>
            <p v-if="record.details" class="text-xs text-muted-foreground mt-0.5">
              {{ record.details }}
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
