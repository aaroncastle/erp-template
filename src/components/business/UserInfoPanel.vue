<script setup lang="ts">
import { User, Settings, LogOut, Bell, Key } from '@lucide/vue'
import Avatar from './Avatar.vue'
import Badge from './Badge.vue'
import DropdownMenu, { type MenuItem } from './DropdownMenu.vue'

export interface UserInfo {
  id: string
  name: string
  employeeNumber: string
  avatar?: string
  department?: string
  role?: string
  unreadCount?: number
}

interface Props {
  user: UserInfo
  showUnreadBadge?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  showUnreadBadge: true,
})

const emit = defineEmits<{
  (e: 'profile'): void
  (e: 'settings'): void
  (e: 'notifications'): void
  (e: 'change-password'): void
  (e: 'logout'): void
}>()

const menuItems: MenuItem[] = [
  { key: 'profile', label: '个人信息', icon: User },
  { key: 'settings', label: '系统设置', icon: Settings },
  { key: 'change-password', label: '修改密码', icon: Key },
  { key: 'divider-1', label: '', divider: true },
  { key: 'logout', label: '退出登录', icon: LogOut, danger: true },
]

function handleSelect(item: MenuItem) {
  switch (item.key) {
    case 'profile':
      emit('profile')
      break
    case 'settings':
      emit('settings')
      break
    case 'notifications':
      emit('notifications')
      break
    case 'change-password':
      emit('change-password')
      break
    case 'logout':
      emit('logout')
      break
  }
}
</script>

<template>
  <div class="flex items-center gap-3">
    <!-- 通知图标 -->
    <button
      class="relative p-2 rounded-md hover:bg-muted transition-colors"
      @click="emit('notifications')"
    >
      <Bell class="h-5 w-5 text-foreground" />
      <Badge
        v-if="showUnreadBadge && user.unreadCount && user.unreadCount > 0"
        :count="user.unreadCount"
        class="absolute -top-1 -right-1"
      />
    </button>

    <!-- 用户菜单 -->
    <DropdownMenu
      :items="menuItems"
      trigger="click"
      align="right"
      width="180px"
      @select="handleSelect"
    >
      <div class="flex items-center gap-2 cursor-pointer p-1 rounded-md hover:bg-muted transition-colors">
        <Avatar
          :src="user.avatar"
          :name="user.name"
          size="sm"
        />
        <div class="hidden md:block">
          <p class="text-sm font-medium text-foreground">{{ user.name }}</p>
          <p class="text-xs text-muted-foreground">{{ user.employeeNumber }}</p>
        </div>
      </div>
    </DropdownMenu>
  </div>
</template>
