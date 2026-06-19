<script setup lang="ts">
import { ref, computed } from 'vue'
import { Button } from '@/components/ui/button'

export type ModuleType = 'quotation' | 'order'
export type StatusType = 'all' | 'pending' | 'in_progress' | 'completed'
export type ManagerFilterType = 'mine' | 'department'

interface Props {
  module?: ModuleType
  status?: StatusType
  managerFilter?: ManagerFilterType
  showManagerFilter?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  module: 'order',
  status: 'all',
  managerFilter: 'mine',
  showManagerFilter: false,
})

const emit = defineEmits<{
  (e: 'update:module', value: ModuleType): void
  (e: 'update:status', value: StatusType): void
  (e: 'update:managerFilter', value: ManagerFilterType): void
}>()

const modules = [
  { key: 'quotation', label: '报价单' },
  { key: 'order', label: '订单' },
]

const statuses = [
  { key: 'all', label: '全部' },
  { key: 'pending', label: '待处理' },
  { key: 'in_progress', label: '进行中' },
  { key: 'completed', label: '已完成' },
]

const managerFilters = [
  { key: 'mine', label: '我的' },
  { key: 'department', label: '全部门' },
]

function setModule(module: ModuleType) {
  emit('update:module', module)
}

function setStatus(status: StatusType) {
  emit('update:status', status)
}

function setManagerFilter(filter: ManagerFilterType) {
  emit('update:managerFilter', filter)
}
</script>

<template>
  <div class="space-y-3">
    <!-- 模块切换 -->
    <div class="flex gap-2">
      <Button
        v-for="mod in modules"
        :key="mod.key"
        variant="outline"
        size="sm"
        :class="module === mod.key ? 'bg-primary text-primary-foreground' : ''"
        @click="setModule(mod.key as ModuleType)"
      >
        {{ mod.label }}
      </Button>
    </div>

    <!-- 状态筛选 -->
    <div class="flex gap-2">
      <Button
        v-for="stat in statuses"
        :key="stat.key"
        variant="ghost"
        size="sm"
        :class="status === stat.key ? 'bg-muted font-medium' : ''"
        @click="setStatus(stat.key as StatusType)"
      >
        {{ stat.label }}
      </Button>
    </div>

    <!-- 经理筛选（仅部门领导可见） -->
    <div v-if="showManagerFilter" class="flex gap-2">
      <Button
        v-for="filter in managerFilters"
        :key="filter.key"
        variant="ghost"
        size="sm"
        :class="managerFilter === filter.key ? 'bg-muted font-medium' : ''"
        @click="setManagerFilter(filter.key as ManagerFilterType)"
      >
        {{ filter.label }}
      </Button>
    </div>
  </div>
</template>
