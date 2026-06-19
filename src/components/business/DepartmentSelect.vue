<script setup lang="ts">
import { ref, computed } from 'vue'
import { Building } from '@lucide/vue'
import { Label } from '@/components/ui/label'

export type DepartmentId = 'sales-1' | 'sales-2' | 'admin-office' | 'accounting' | 'warehouse' | 'finance'

export interface Department {
  id: DepartmentId
  name: string
  description?: string
}

interface Props {
  modelValue?: DepartmentId
  departments?: Department[]
  label?: string
  placeholder?: string
}

const props = withDefaults(defineProps<Props>(), {
  departments: () => [
    { id: 'sales-1', name: '销售一部', description: '销售业务' },
    { id: 'sales-2', name: '销售二部', description: '销售业务（与销售一部 UI 共享，数据隔离）' },
    { id: 'admin-office', name: '综合办', description: '标记、标准库匹配、订单修改 diff' },
    { id: 'accounting', name: '核算部', description: '成本计算、膨胀系数管理' },
    { id: 'warehouse', name: '仓储部', description: '入库管理' },
    { id: 'finance', name: '财务部', description: '开票、支付管理' },
  ],
  label: '部门',
  placeholder: '请选择部门',
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: DepartmentId): void
  (e: 'change', department: Department): void
}>()

function handleChange(event: Event) {
  const target = event.target as HTMLSelectElement
  const value = target.value as DepartmentId
  emit('update:modelValue', value)

  const department = props.departments.find(d => d.id === value)
  if (department) {
    emit('change', department)
  }
}
</script>

<template>
  <div class="space-y-2">
    <Label v-if="label">{{ label }}</Label>
    <div class="relative">
      <select
        :value="modelValue"
        class="w-full px-3 py-2 pr-10 border border-input rounded-md bg-background text-sm focus:outline-none focus:ring-2 focus:ring-ring"
        @change="handleChange"
      >
        <option value="" disabled>{{ placeholder }}</option>
        <option
          v-for="dept in departments"
          :key="dept.id"
          :value="dept.id"
        >
          {{ dept.name }}
        </option>
      </select>
      <Building class="absolute right-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground pointer-events-none" />
    </div>
  </div>
</template>
