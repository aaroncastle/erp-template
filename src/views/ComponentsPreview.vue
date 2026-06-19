<script setup lang="ts">
import { ref } from 'vue'

// Phase 1 组件导入
import UserSelect, { type UserInfo } from '@/components/business/UserSelect.vue'
import DepartmentSelect from '@/components/business/DepartmentSelect.vue'
import RoleSelect from '@/components/business/RoleSelect.vue'
import AlertBanner from '@/components/business/AlertBanner.vue'
import VerifyCodeInput from '@/components/business/VerifyCodeInput.vue'

// Phase 1 模拟数据
const mockUsers = ref<UserInfo[]>([
  {
    id: '1',
    employeeNumber: 'EMP001',
    name: '张三',
    department: '销售一部',
    role: 'department-user',
  },
  {
    id: '2',
    employeeNumber: 'EMP002',
    name: '李四',
    department: '综合办',
    role: 'department-user',
  },
  {
    id: '3',
    employeeNumber: 'EMP003',
    name: '王五',
    department: '财务部',
    role: 'department-user',
  },
  {
    id: '4',
    employeeNumber: 'ADMIN001',
    name: '郝小领',
    role: 'super-admin',
  },
])

const showUserSelect = ref(false)

function handleUserSelect(user: UserInfo) {
  console.log('选择用户:', user)
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 1 - 认证与权限组件</p>
      </div>

      <!-- Phase 1 组件预览 -->

      <!-- 用户选择器 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">用户选择器</h2>
            <p class="text-sm text-muted-foreground mt-1">
              用于审批人选择、客户分配、用户管理
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showUserSelect = true"
          >
            打开用户选择器
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 搜索用户（姓名/工号/部门）</p>
          <p>• 显示用户详细信息</p>
          <p>• 选中状态标记</p>
        </div>
      </div>

      <!-- 部门选择器 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">部门选择器</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用于用户创建、数据筛选
          </p>
        </div>
        <DepartmentSelect
          @change="(dept) => console.log('选择部门:', dept)"
        />
      </div>

      <!-- 角色选择器 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">角色选择器</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用于用户创建、权限配置
          </p>
        </div>
        <RoleSelect
          @change="(role) => console.log('选择角色:', role)"
        />
      </div>

      <!-- 提醒横幅 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">提醒横幅</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用于"请绑定手机号"等提醒
          </p>
        </div>
        <AlertBanner
          type="warning"
          message="请绑定手机号以启用密码找回和短信通知功能"
          show-close
        />
      </div>

      <!-- 验证码输入框 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">验证码输入框</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用于密码重置、短信验证
          </p>
        </div>
        <VerifyCodeInput />
      </div>

      <!-- 组件实例 -->
      <UserSelect
        v-model:open="showUserSelect"
        :users="mockUsers"
        @select="handleUserSelect"
      />
    </div>
  </div>
</template>
