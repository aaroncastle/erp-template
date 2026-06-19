<script setup lang="ts">
import { ref, computed, watch, nextTick } from 'vue'
import { Search, User, X } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Card, CardContent } from '@/components/ui/card'

export interface UserInfo {
  id: string
  employeeNumber: string
  name: string
  department?: string
  role?: string
  avatar?: string
}

interface Props {
  open?: boolean
  users?: UserInfo[]
  placeholder?: string
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
  users: () => [],
  placeholder: '搜索用户...',
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'select', user: UserInfo): void
}>()

const searchQuery = ref('')
const selectedId = ref<string>('')
const searchInput = ref<HTMLInputElement | null>(null)

// 监听打开状态，自动聚焦
watch(() => props.open, (value) => {
  if (value) {
    nextTick(() => {
      const input = searchInput.value?.$el as HTMLInputElement
      input?.focus()
    })
  }
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

// 选择用户
function selectUser(user: UserInfo) {
  selectedId.value = user.id
  emit('select', user)
  close()
}

// 关闭
function close() {
  emit('update:open', false)
  searchQuery.value = ''
  selectedId.value = ''
}
</script>

<template>
  <Teleport to="body">
    <div
      v-if="open"
      class="fixed inset-0 z-[200] bg-black/50 backdrop-blur-sm flex items-center justify-center"
      @click.self="close"
    >
      <Card class="w-[500px] max-h-[80vh] flex flex-col">
        <div class="p-4 border-b border-border">
          <div class="flex items-center justify-between mb-3">
            <h2 class="text-lg font-semibold text-foreground">选择用户</h2>
            <Button variant="ghost" size="sm" @click="close">
              <X class="h-4 w-4" />
            </Button>
          </div>

          <!-- 搜索框 -->
          <div class="relative">
            <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
            <Input
              ref="searchInput"
              v-model="searchQuery"
              :placeholder="placeholder"
              class="pl-10"
            />
          </div>
        </div>

        <CardContent class="flex-1 overflow-y-auto p-4">
          <!-- 用户列表 -->
          <div v-if="filteredUsers.length > 0" class="space-y-1">
            <div
              v-for="user in filteredUsers"
              :key="user.id"
              class="flex items-center justify-between p-3 rounded-md hover:bg-accent cursor-pointer transition-colors border border-border"
              @click="selectUser(user)"
            >
              <div class="flex items-center gap-3 flex-1">
                <div class="h-10 w-10 rounded-full bg-primary/10 flex items-center justify-center">
                  <User class="h-5 w-5 text-primary" />
                </div>
                <div class="flex-1">
                  <div class="flex items-center gap-2">
                    <span class="text-sm font-medium text-foreground">{{ user.name }}</span>
                    <span class="text-xs text-muted-foreground">{{ user.employeeNumber }}</span>
                  </div>
                  <div v-if="user.department || user.role" class="text-xs text-muted-foreground mt-1">
                    <span v-if="user.department">{{ user.department }}</span>
                    <span v-if="user.department && user.role"> · </span>
                    <span v-if="user.role">{{ user.role }}</span>
                  </div>
                </div>
              </div>
              <div
                v-if="selectedId === user.id"
                class="h-2 w-2 rounded-full bg-primary"
              />
            </div>
          </div>

          <!-- 空状态 -->
          <div v-else class="text-center py-8">
            <User class="h-12 w-12 mx-auto text-muted-foreground mb-2" />
            <p class="text-muted-foreground">
              {{ searchQuery ? '未找到匹配的用户' : '暂无用户数据' }}
            </p>
          </div>
        </CardContent>
      </Card>
    </div>
  </Teleport>
</template>
