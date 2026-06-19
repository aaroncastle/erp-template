<script setup lang="ts">
import { ref, computed } from 'vue'
import { User, Plus, Edit, Trash2, Lock, Unlock, Search } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Card, CardContent } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'
import EmptyState from './EmptyState.vue'
import Modal from './Modal.vue'
import UserSelect from './UserSelect.vue'
import DepartmentSelect from './DepartmentSelect.vue'
import RoleSelect from './RoleSelect.vue'

export type UserStatus = 'enabled' | 'disabled' | 'locked' | 'deleted'

export interface User {
  id: string
  employeeNumber: string
  name: string
  phone?: string
  role: 'super-admin' | 'system-admin' | 'department-user'
  department?: string
  status: UserStatus
  isManager?: boolean
  createdAt: string
  lastLogin?: string
}

interface Props {
  users?: User[]
  currentRole?: 'super-admin' | 'system-admin'
}

const props = withDefaults(defineProps<Props>(), {
  users: () => [],
  currentRole: 'super-admin',
})

const emit = defineEmits<{
  (e: 'create', user: Partial<User>): void
  (e: 'edit', user: User): void
  (e: 'delete', user: User): void
  (e: 'enable', user: User): void
  (e: 'disable', user: User): void
  (e: 'unlock', user: User): void
}>()

const searchQuery = ref('')
const showCreateModal = ref(false)
const showUserSelect = ref(false)

// 新建用户表单
const newUser = ref<Partial<User>>({
  employeeNumber: '',
  name: '',
  phone: '',
  role: 'department-user',
  department: undefined,
})

// 过滤用户列表
const filteredUsers = computed(() => {
  if (!searchQuery.value.trim()) return props.users

  const query = searchQuery.value.toLowerCase()
  return props.users.filter(
    (user) =>
      user.name.toLowerCase().includes(query) ||
      user.employeeNumber.toLowerCase().includes(query) ||
      user.department?.toLowerCase().includes(query)
  )
})

const statusConfig = {
  enabled: { label: '启用', class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300' },
  disabled: { label: '停用', class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300' },
  locked: { label: '锁定', class: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300' },
  deleted: { label: '已删除', class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300' },
}

// 创建用户
function handleCreate() {
  if (!newUser.value.employeeNumber || !newUser.value.name) {
    alert('请填写必填字段')
    return
  }
  emit('create', newUser.value)
  showCreateModal.value = false
  newUser.value = {
    employeeNumber: '',
    name: '',
    phone: '',
    role: 'department-user',
    department: undefined,
  }
}

// 选择用户（用于分配客户等）
function handleSelectUser(user: any) {
  console.log('选择用户:', user)
  showUserSelect.value = false
}
</script>

<template>
  <Card>
    <CardContent class="p-4">
      <!-- 头部 -->
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold text-foreground">用户管理</h3>
        <Button @click="showCreateModal = true">
          <Plus class="h-4 w-4 mr-2" />
          创建用户
        </Button>
      </div>

      <!-- 搜索 -->
      <div class="relative mb-4">
        <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          v-model="searchQuery"
          placeholder="搜索用户姓名、工号或部门..."
          class="pl-10"
        />
      </div>

      <!-- 用户列表 -->
      <div v-if="filteredUsers.length > 0" class="space-y-2">
        <div
          v-for="user in filteredUsers"
          :key="user.id"
          class="flex items-center justify-between p-3 border border-border rounded-lg hover:bg-muted/30 transition-colors"
        >
          <div class="flex items-center gap-3 flex-1">
            <div class="h-10 w-10 rounded-full bg-primary/10 flex items-center justify-center">
              <User class="h-5 w-5 text-primary" />
            </div>
            <div class="flex-1">
              <div class="flex items-center gap-2">
                <h4 class="text-sm font-medium text-foreground">{{ user.name }}</h4>
                <span class="text-xs text-muted-foreground">{{ user.employeeNumber }}</span>
                <span
                  v-if="user.isManager"
                  class="px-2 py-0.5 text-xs rounded-md bg-primary/10 text-primary"
                >
                  部门领导
                </span>
              </div>
              <div class="flex items-center gap-4 mt-1 text-xs text-muted-foreground">
                <span v-if="user.department">部门：{{ user.department }}</span>
                <span>角色：{{ user.role }}</span>
                <span v-if="user.phone">电话：{{ user.phone }}</span>
              </div>
              <div class="flex items-center gap-4 mt-1 text-xs text-muted-foreground">
                <span>创建：{{ user.createdAt }}</span>
                <span v-if="user.lastLogin">最后登录：{{ user.lastLogin }}</span>
              </div>
            </div>
          </div>
          <div class="flex items-center gap-2">
            <StatusBadge :status="user.status" />
            <div class="flex items-center gap-1">
              <Button variant="ghost" size="sm" @click="emit('edit', user)">
                <Edit class="h-4 w-4" />
              </Button>
              <Button
                v-if="user.status === 'locked'"
                variant="ghost"
                size="sm"
                @click="emit('unlock', user)"
              >
                <Unlock class="h-4 w-4 text-green-600" />
              </Button>
              <Button
                v-if="user.status === 'enabled'"
                variant="ghost"
                size="sm"
                @click="emit('disable', user)"
              >
                <Lock class="h-4 w-4 text-yellow-600" />
              </Button>
              <Button
                v-if="user.status === 'disabled'"
                variant="ghost"
                size="sm"
                @click="emit('enable', user)"
              >
                <Unlock class="h-4 w-4 text-green-600" />
              </Button>
              <Button
                v-if="user.status !== 'deleted'"
                variant="ghost"
                size="sm"
                @click="emit('delete', user)"
              >
                <Trash2 class="h-4 w-4 text-destructive" />
              </Button>
            </div>
          </div>
        </div>
      </div>

      <!-- 空状态 -->
      <EmptyState
        v-else
        type="search"
        title="未找到用户"
        description="尝试使用其他搜索条件"
      />
    </CardContent>

    <!-- 创建用户模态框 -->
    <Modal
      v-model:open="showCreateModal"
      title="创建用户"
      width="md"
    >
      <div class="space-y-4">
        <div class="space-y-2">
          <label class="text-sm font-medium text-foreground">工号 *</label>
          <Input
            v-model="newUser.employeeNumber"
            placeholder="输入工号"
          />
        </div>
        <div class="space-y-2">
          <label class="text-sm font-medium text-foreground">姓名 *</label>
          <Input
            v-model="newUser.name"
            placeholder="输入姓名"
          />
        </div>
        <div class="space-y-2">
          <label class="text-sm font-medium text-foreground">手机号</label>
          <Input
            v-model="newUser.phone"
            placeholder="输入手机号（可后置填充）"
          />
        </div>
        <div class="space-y-2">
          <RoleSelect
            v-model="newUser.role"
            :can-create-super-admin="currentRole === 'super-admin'"
          />
        </div>
        <div v-if="newUser.role === 'department-user'" class="space-y-2">
          <DepartmentSelect v-model="newUser.department" />
        </div>
        <div class="text-xs text-muted-foreground">
          初始密码：123123（首次登录强制修改）
        </div>
      </div>
      <template #footer>
        <div class="flex items-center justify-end gap-2">
          <Button variant="outline" @click="showCreateModal = false">
            取消
          </Button>
          <Button @click="handleCreate">
            创建
          </Button>
        </div>
      </template>
    </Modal>

    <!-- 用户选择器（用于分配客户等） -->
    <UserSelect
      v-model:open="showUserSelect"
      :users="users"
      @select="handleSelectUser"
    />
  </Card>
</template>
