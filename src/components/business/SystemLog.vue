<script setup lang="ts">
import { ref, computed } from 'vue'
import { FileText, Search, Calendar, User } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'
import EmptyState from './EmptyState.vue'
import DataTable, { type Column } from './DataTable.vue'

export type LogLevel = 'info' | 'warning' | 'error' | 'debug'

export interface SystemLog {
  id: string
  timestamp: string
  level: LogLevel
  module: string
  action: string
  user?: string
  message: string
  ip?: string
  details?: string
}

interface Props {
  logs?: SystemLog[]
}

const props = withDefaults(defineProps<Props>(), {
  logs: () => [
    {
      id: '1',
      timestamp: '2026-06-19 10:30:15',
      level: 'info',
      module: '订单',
      action: '创建订单',
      user: '张三',
      message: '创建订单 ORD-20260619-001',
      ip: '192.168.1.100',
    },
    {
      id: '2',
      timestamp: '2026-06-19 10:32:20',
      level: 'warning',
      module: '报价单',
      action: '提交审批',
      user: '李四',
      message: '报价单 QUO-20260619-001 提交审批，金额超过阈值',
      ip: '192.168.1.101',
    },
    {
      id: '3',
      timestamp: '2026-06-19 10:35:10',
      level: 'error',
      module: '开票',
      action: '开具发票',
      user: '王五',
      message: '开具发票失败：税务系统连接超时',
      ip: '192.168.1.102',
    },
    {
      id: '4',
      timestamp: '2026-06-19 10:40:05',
      level: 'info',
      module: '仓储',
      action: '入库',
      user: '赵六',
      message: '订单 ORD-20260615-001 入库完成',
      ip: '192.168.1.103',
    },
  ],
})

const searchQuery = ref('')
const selectedLevel = ref<string>('all')

// 表格列定义
const columns: Column<SystemLog>[] = [
  { key: 'timestamp', label: '时间', width: '180px', sortable: true },
  { key: 'level', label: '级别', width: '100px', sortable: true },
  { key: 'module', label: '模块', width: '100px', sortable: true },
  { key: 'action', label: '操作', width: '120px' },
  { key: 'user', label: '用户', width: '100px' },
  { key: 'message', label: '消息', sortable: false },
]

// 过滤日志
const filteredLogs = computed(() => {
  let logs = [...props.logs]

  // 级别过滤
  if (selectedLevel.value !== 'all') {
    logs = logs.filter(log => log.level === selectedLevel.value)
  }

  // 搜索过滤
  if (searchQuery.value.trim()) {
    const query = searchQuery.value.toLowerCase()
    logs = logs.filter(log =>
      log.message.toLowerCase().includes(query) ||
      log.user?.toLowerCase().includes(query) ||
      log.module.toLowerCase().includes(query) ||
      log.action.toLowerCase().includes(query)
    )
  }

  return logs
})

const levelConfig = {
  info: { label: '信息', class: 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-300' },
  warning: { label: '警告', class: 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900/30 dark:text-yellow-300' },
  error: { label: '错误', class: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300' },
  debug: { label: '调试', class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300' },
}
</script>

<template>
  <Card>
    <CardHeader>
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <FileText class="h-5 w-5 text-primary" />
          <CardTitle>系统日志</CardTitle>
        </div>
      </div>
    </CardHeader>
    <CardContent>
      <!-- 筛选 -->
      <div class="flex items-center gap-4 mb-4">
        <div class="relative flex-1">
          <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
          <Input
            v-model="searchQuery"
            placeholder="搜索日志..."
            class="pl-10"
          />
        </div>
        <div class="flex items-center gap-2">
          <button
            class="px-3 py-1.5 text-xs rounded-md transition-colors"
            :class="selectedLevel === 'all' ? 'bg-primary text-primary-foreground' : 'bg-muted hover:bg-muted/80'"
            @click="selectedLevel = 'all'"
          >
            全部
          </button>
          <button
            v-for="(config, level) in levelConfig"
            :key="level"
            class="px-3 py-1.5 text-xs rounded-md transition-colors"
            :class="selectedLevel === level ? 'bg-primary text-primary-foreground' : 'bg-muted hover:bg-muted/80'"
            @click="selectedLevel = level"
          >
            {{ config.label }}
          </button>
        </div>
      </div>

      <!-- 日志列表 -->
      <DataTable
        :columns="columns"
        :data="filteredLogs"
        :page-size="10"
        search-placeholder="搜索日志..."
        @row-click="(log) => console.log('查看日志详情:', log)"
      >
        <template #cell-level="{ value }">
          <StatusBadge :status="value" />
        </template>
      </DataTable>
    </CardContent>
  </Card>
</template>
