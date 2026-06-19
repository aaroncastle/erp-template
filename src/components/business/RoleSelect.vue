<script setup lang="ts">
import { ref, computed } from 'vue'
import { Shield } from '@lucide/vue'
import { Label } from '@/components/ui/label'

export type RoleType = 'super-admin' | 'system-admin' | 'department-user'

export interface Role {
  id: RoleType
  name: string
  description?: string
}

interface Props {
  modelValue?: RoleType
  roles?: Role[]
  label?: string
  placeholder?: string
  canCreateSuperAdmin?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  roles: () => [
    { id: 'super-admin', name: '超级管理员', description: '拥有系统全部功能权限' },
    { id: 'system-admin', name: '系统管理员', description: '管理用户、业务配置、系统运维' },
    { id: 'department-user', name: '部门用户', description: '绑定部门，只能看到本部门数据' },
  ],
  label: '角色',
  placeholder: '请选择角色',
  canCreateSuperAdmin: false,
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: RoleType): void
  (e: 'change', role: Role): void
}>()

// 过滤角色列表（根据权限）
const availableRoles = computed(() => {
  if (props.canCreateSuperAdmin) {
    return props.roles
  }
  return props.roles.filter(r => r.id !== 'super-admin')
})

function handleChange(event: Event) {
  const target = event.target as HTMLSelectElement
  const value = target.value as RoleType
  emit('update:modelValue', value)

  const role = props.roles.find(r => r.id === value)
  if (role) {
    emit('change', role)
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
          v-for="role in availableRoles"
          :key="role.id"
          :value="role.id"
        >
          {{ role.name }}
        </option>
      </select>
      <Shield class="absolute right-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground pointer-events-none" />
    </div>
    <p v-if="!canCreateSuperAdmin" class="text-xs text-muted-foreground">
      仅超级管理员可创建超级管理员账号
    </p>
  </div>
</template>
