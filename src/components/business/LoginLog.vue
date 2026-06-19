<script setup lang="ts">
import { ref, computed } from 'vue'
import { LogIn, Search, Calendar, User } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'
import EmptyState from './EmptyState.vue'
import DataTable, { type Column } from './DataTable.vue'

export type LoginStatus = 'success' | 'failed' | 'locked'

export interface LoginLog {
  id: string
  timestamp: string
  user: string
  employeeNumber: string
  status: LoginStatus
  ip: string
  location?: string
  device?: string
  browser?: string
  failReason?: string
}

interface Props {
  logs?: LoginLog[]
}

const props = withDefaults(defineProps<Props>(), {
  logs: () => [
    {
      id: '1',
      timestamp: '2026-06-19 09:15:30',
      user: '张三',
      employeeNumber: 'EMP001',
      status: 'success',
      ip: '192.168.1.100',
      location: '河南省洛阳市',
      device: 'Windows 10',
      browser: 'Chrome 120',
    },
    {
      id: '2',
      timestamp: '2026-06-19 09:20:15',
      user: '李四',
      employeeNumber: 'EMP002',
      status: 'failed',
      ip: '192.168.1.101',
      location: '河南省洛阳市',
      device: 'Windows 10',
      browser: 'Chrome 120',
      failReason: '密码错误',
    },
    {
      id: '3',
      timestamp: '2026-06-19 09:25:40',
      user: '李四',
      employeeNumber: 'EMP002',
      status: 'failed',
      ip: '192.168.1.101',
      location: '河南省洛阳市',
      device: 'Windows 10',
      browser: 'Chrome 120',
      failReason: '密码错误',
    },
    {
      id: '4',
      timestamp: '2026-06-19 09:30:05',
      user: '李四',
      employeeNumber: 'EMP002',
      status: 'locked',
      ip: '192.168.1.101',
      location: '河南省洛阳市',
      device: 'Windows 10',
      browser: 'Chrome 120',
      failReason: '连续5次密码错误，账号已锁定',
    },
    {
      id: '5',
      timestamp: '2026-06-19 09:35:20',
      user: '王五',
      employeeNumber: 'EMP003',
      status: 'success',
      ip: '192.168.1.102',
      location: '河南省郑州市',
      device: 'macOS',
      browser: 'Safari 17',
    },
  ],
})

const searchQuery = ref('')
const selectedStatus = ref<string>('all')

// 表格列定义
const columns: Column<LoginLog>[] = [
  { key: 'timestamp', label: '时间', width: '180px', sortable: true },
  { key: 'user', label: '用户', width: '100px', sortable: true },
  { key: 'employeeNumber', label: '工号', width: '100px' },
  { key: 'status', label: '状态', width: '100px', sortable: true },
  { key: 'ip', label: 'IP地址', width: '130px' },
  { key: 'location', label: '地点', width: '120px' },
  { key: 'device', label: '设备', width: '100px' },
  { key: 'browser', label: '浏览器', width: '100px' },
]

// 过滤日志
const filteredLogs = computed(() => {
  let logs = [...props.logs]

  // 状态过滤
  if (selectedStatus.value !== 'all') {
    logs = logs.filter(log => log.status === selectedStatus.value)
  }

  // 搜索过滤
  if (searchQuery.value.trim()) {
    const query = searchQuery.value.toLowerCase()
    logs = logs.filter(log =>
      log.user.toLowerCase().includes(query) ||
      log.employeeNumber.toLowerCase().includes(query) ||
      log.ip.includes(query) ||
      log.location?.toLowerCase().includes(query)
    )
  }

  return logs
})

const statusConfig = {
  success: { label: '成功', class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300' },
  failed: { label: '失败', class: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300' },
  locked: { label: '锁定', class: 'bg-yellow-100 text-yellow-800 dark:bg-yellow-900/30 dark:text-yellow-300' },
}
</script>

<template>
  <Card>
    <CardHeader>
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <LogIn class="h-5 w-5 text-primary" />
          <CardTitle>登录日志</CardTitle>
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
            placeholder="搜索用户、工号、IP..."
            class="pl-10"
          />
        </div>
        <div class="flex items-center gap-2">
          <button
            class="px-3 py-1.5 text-xs rounded-md transition-colors"
            :class="selectedStatus === 'all' ? 'bg-primary text-primary-foreground' : 'bg-muted hover:bg-muted/80'"
            @click="selectedStatus = 'all'"
          >
            全部
          </button>
          <button
            v-for="(config, status) in statusConfig"
            :key="status"
            class="px-3 py-1.5 text-xs rounded-md transition-colors"
            :class="selectedStatus === status ? 'bg-primary text-primary-foreground' : 'bg-muted hover:bg-muted/80'"
            @click="selectedStatus = status"
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
        search-placeholder="搜索登录日志..."
        @row-click="(log) => console.log('查看登录日志详情:', log)"
      >
        <template #cell-status="{ value }">
          <StatusBadge :status="value" />
        </template>
      </DataTable>
    </CardContent>
  </Card>
</template>
