<script setup lang="ts">
import { ref } from 'vue'
import LoginForm from '@/components/business/LoginForm.vue'
import ChangePasswordForm from '@/components/business/ChangePasswordForm.vue'
import Avatar from '@/components/business/Avatar.vue'
import Badge from '@/components/business/Badge.vue'
import DropdownMenu, { type MenuItem } from '@/components/business/DropdownMenu.vue'
import UserInfoPanel, { type UserInfo } from '@/components/business/UserInfoPanel.vue'

// 模拟用户数据
const mockUser = ref<UserInfo>({
  id: '1',
  name: '张三',
  employeeNumber: 'EMP001',
  department: '销售一部',
  role: 'department-user',
})

// 模拟登录表单
const loginLoading = ref(false)
const loginError = ref('')

function handleLogin(credentials: { employeeNumber: string; password: string }) {
  console.log('登录:', credentials)
  loginLoading.value = true
  setTimeout(() => {
    loginLoading.value = false
    loginError.value = '工号或密码错误'
  }, 1000)
}

// 模拟修改密码
const passwordLoading = ref(false)
const passwordError = ref('')

function handleChangePassword(data: any) {
  console.log('修改密码:', data)
  passwordLoading.value = true
  setTimeout(() => {
    passwordLoading.value = false
  }, 1000)
}

// 模拟下拉菜单
const dropdownItems: MenuItem[] = [
  { key: 'item1', label: '选项 1' },
  { key: 'item2', label: '选项 2' },
  { key: 'divider', label: '', divider: true },
  { key: 'item3', label: '选项 3', disabled: true },
  { key: 'item4', label: '危险操作', danger: true },
]

function handleMenuSelect(item: MenuItem) {
  console.log('选择菜单项:', item)
}

// 用户信息面板事件
function handleProfile() {
  console.log('查看个人信息')
}

function handleSettings() {
  console.log('系统设置')
}

function handleLogout() {
  console.log('退出登录')
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 5 - 认证完善组件</p>
      </div>

      <!-- Phase 5 组件预览 -->

      <!-- 登录表单 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">登录表单</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用户登录界面
          </p>
        </div>

        <div class="max-w-sm mx-auto">
          <LoginForm
            :loading="loginLoading"
            :error="loginError"
            @submit="handleLogin"
            @forgot-password="() => console.log('忘记密码')"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 工号/密码输入</p>
          <p>• 密码显示/隐藏切换</p>
          <p>• 加载状态</p>
          <p>• 错误提示</p>
          <p>• 忘记密码链接</p>
        </div>
      </div>

      <!-- 修改密码表单 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">修改密码表单</h2>
          <p class="text-sm text-muted-foreground mt-1">
            首次登录或修改密码
          </p>
        </div>

        <div class="max-w-sm mx-auto">
          <ChangePasswordForm
            :loading="passwordLoading"
            :error="passwordError"
            :require-old-password="true"
            @submit="handleChangePassword"
            @cancel="() => console.log('取消修改')"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 旧密码验证</p>
          <p>• 新密码强度检查</p>
          <p>• 确认密码匹配</p>
          <p>• 密码显示/隐藏切换</p>
          <p>• 实时验证提示</p>
        </div>
      </div>

      <!-- 头像组件 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">头像组件</h2>
          <p class="text-sm text-muted-foreground mt-1">
            用户头像展示
          </p>
        </div>

        <div class="flex items-center gap-4">
          <Avatar name="张三" size="sm" />
          <Avatar name="李四" size="md" />
          <Avatar name="王五" size="lg" />
          <Avatar name="赵六" size="xl" />
          <Avatar size="md" />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 多种尺寸（sm/md/lg/xl）</p>
          <p>• 支持图片/首字母/默认图标</p>
          <p>• 圆形/方形形状</p>
          <p>• 基于名字生成背景色</p>
        </div>
      </div>

      <!-- 徽章组件 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">徽章组件</h2>
          <p class="text-sm text-muted-foreground mt-1">
            通知数量/状态标记
          </p>
        </div>

        <div class="flex items-center gap-8">
          <Badge :count="5">
            <div class="h-10 w-10 rounded-md bg-muted" />
          </Badge>
          <Badge :count="99" max-count="99">
            <div class="h-10 w-10 rounded-md bg-muted" />
          </Badge>
          <Badge :count="100" max-count="99">
            <div class="h-10 w-10 rounded-md bg-muted" />
          </Badge>
          <Badge dot>
            <div class="h-10 w-10 rounded-md bg-muted" />
          </Badge>
          <Badge :count="0" show-zero>
            <div class="h-10 w-10 rounded-md bg-muted" />
          </Badge>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 数字显示</p>
          <p>• 最大值限制（99+）</p>
          <p>• 红点模式</p>
          <p>• 多种颜色类型</p>
          <p>• 显示零值选项</p>
        </div>
      </div>

      <!-- 下拉菜单 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">下拉菜单</h2>
          <p class="text-sm text-muted-foreground mt-1">
            操作菜单
          </p>
        </div>

        <div class="flex items-center gap-4">
          <DropdownMenu
            :items="dropdownItems"
            align="left"
            @select="handleMenuSelect"
          >
            <button class="px-4 py-2 rounded-md bg-primary text-primary-foreground">
              点击打开
            </button>
          </DropdownMenu>

          <DropdownMenu
            :items="dropdownItems"
            align="right"
            @select="handleMenuSelect"
          >
            <button class="px-4 py-2 rounded-md bg-muted">
              右对齐菜单
            </button>
          </DropdownMenu>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 点击触发</p>
          <p>• 左/右对齐</p>
          <p>• 禁用项</p>
          <p>• 危险操作项</p>
          <p>• 分隔线</p>
          <p>• 点击外部关闭</p>
          <p>• ESC键关闭</p>
          <p>• 动画效果</p>
        </div>
      </div>

      <!-- 用户信息面板 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">用户信息面板</h2>
          <p class="text-sm text-muted-foreground mt-1">
            Header用户菜单
          </p>
        </div>

        <div class="flex items-center justify-end p-4 bg-muted rounded-lg">
          <UserInfoPanel
            :user="mockUser"
            @profile="handleProfile"
            @settings="handleSettings"
            @change-password="() => console.log('修改密码')"
            @logout="handleLogout"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 用户头像显示</p>
          <p>• 姓名和工号显示</p>
          <p>• 下拉菜单（个人信息/设置/修改密码/退出）</p>
        </div>
      </div>
    </div>
  </div>
</template>
