<script setup lang="ts">
import { ref } from 'vue'
import UserManagement, { type User } from '@/components/business/UserManagement.vue'
import PermissionConfig, { type Permission } from '@/components/business/PermissionConfig.vue'
import ApprovalConfig, { type ApprovalFlow } from '@/components/business/ApprovalConfig.vue'
import SystemLog, { type SystemLog as SystemLogType } from '@/components/business/SystemLog.vue'
import LoginLog, { type LoginLog as LoginLogType } from '@/components/business/LoginLog.vue'

// 模拟用户数据
const mockUsers = ref<User[]>([
  {
    id: '1',
    employeeNumber: 'EMP001',
    name: '张三',
    phone: '138****1234',
    role: 'department-user',
    department: '销售一部',
    status: 'enabled',
    isManager: true,
    createdAt: '2026-01-15',
    lastLogin: '2026-06-19 09:15',
  },
  {
    id: '2',
    employeeNumber: 'EMP002',
    name: '李四',
    phone: '139****5678',
    role: 'department-user',
    department: '综合办',
    status: 'locked',
    createdAt: '2026-02-20',
    lastLogin: '2026-06-18 14:30',
  },
  {
    id: '3',
    employeeNumber: 'ADMIN001',
    name: '郝小领',
    phone: '173****8865',
    role: 'super-admin',
    status: 'enabled',
    createdAt: '2026-01-01',
    lastLogin: '2026-06-19 10:00',
  },
])

function handleCreateUser(user: Partial<User>) {
  console.log('创建用户:', user)
}

function handleEditUser(user: User) {
  console.log('编辑用户:', user)
}

function handleDeleteUser(user: User) {
  console.log('删除用户:', user)
}

function handleEnableUser(user: User) {
  console.log('启用用户:', user)
}

function handleDisableUser(user: User) {
  console.log('停用用户:', user)
}

function handleUnlockUser(user: User) {
  console.log('解锁用户:', user)
}

function handleSavePermissions(permissions: Permission[]) {
  console.log('保存权限:', permissions)
}

function handleSaveFlows(flows: ApprovalFlow[]) {
  console.log('保存审批流程:', flows)
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-7xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 4 - 管理组件</p>
      </div>

      <!-- Phase 4 组件预览 -->

      <!-- 用户管理 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">用户管理</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用户账号管理，创建/编辑/启用/停用/解锁
          </p>
        </div>

        <UserManagement
          :users="mockUsers"
          current-role="super-admin"
          @create="handleCreateUser"
          @edit="handleEditUser"
          @delete="handleDeleteUser"
          @enable="handleEnableUser"
          @disable="handleDisableUser"
          @unlock="handleUnlockUser"
        />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 用户列表展示</p>
          <p>• 创建用户（工号、姓名、手机号、角色、部门）</p>
          <p>• 编辑用户信息</p>
          <p>• 启用/停用/解锁用户</p>
          <p>• 部门领导标识</p>
        </div>
      </div>

      <!-- 权限配置 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">权限配置</h2>
          <p class="text-sm text-muted-foreground mt-1">
            配置各模块权限
          </p>
        </div>

        <PermissionConfig @save="handleSavePermissions" />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 按模块分组权限</p>
          <p>• 权限开关控制</p>
          <p>• 权限描述说明</p>
          <p>• 保存配置</p>
        </div>
      </div>

      <!-- 审批流程配置 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">审批流程配置</h2>
          <p class="text-sm text-muted-foreground mt-1">
            配置各业务类型的审批流程
          </p>
        </div>

        <ApprovalConfig @save="handleSaveFlows" />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 添加/删除审批流程</p>
          <p>• 配置审批步骤</p>
          <p>• 选择审批人</p>
          <p>• 流程启用/停用</p>
        </div>
      </div>

      <!-- 系统日志 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">系统日志</h2>
          <p class="text-sm text-muted-foreground mt-1">
            系统操作日志查看
          </p>
        </div>

        <SystemLog />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 日志级别筛选</p>
          <p>• 搜索过滤</p>
          <p>• 分页展示</p>
          <p>• 日志详情查看</p>
        </div>
      </div>

      <!-- 登录日志 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">登录日志</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用户登录记录查看
          </p>
        </div>

        <LoginLog />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 登录状态筛选</p>
          <p>• 搜索过滤</p>
          <p>• 分页展示</p>
          <p>• 登录详情（IP、设备、浏览器、地点）</p>
        </div>
      </div>
    </div>
  </div>
</template>
