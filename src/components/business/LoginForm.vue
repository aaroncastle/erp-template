<script setup lang="ts">
import { ref, computed } from 'vue'
import { User, Lock, Eye, EyeOff } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Label } from '@/components/ui/label'

interface Props {
  loading?: boolean
  error?: string
}

const props = withDefaults(defineProps<Props>(), {
  loading: false,
  error: '',
})

const emit = defineEmits<{
  (e: 'submit', credentials: { employeeNumber: string; password: string }): void
}>()

const employeeNumber = ref('')
const password = ref('')
const showPassword = ref(false)

const canSubmit = computed(() => {
  return employeeNumber.value.trim() && password.value.trim() && !props.loading
})

function handleSubmit() {
  if (!canSubmit.value) return
  emit('submit', {
    employeeNumber: employeeNumber.value,
    password: password.value,
  })
}

function togglePassword() {
  showPassword.value = !showPassword.value
}
</script>

<template>
  <form @submit.prevent="handleSubmit" class="space-y-4">
    <!-- 工号 -->
    <div class="space-y-2">
      <Label for="employeeNumber">工号</Label>
      <div class="relative">
        <User class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          id="employeeNumber"
          v-model="employeeNumber"
          type="text"
          placeholder="请输入工号"
          class="pl-10"
          :disabled="loading"
          autocomplete="username"
        />
      </div>
    </div>

    <!-- 密码 -->
    <div class="space-y-2">
      <Label for="password">密码</Label>
      <div class="relative">
        <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          id="password"
          v-model="password"
          :type="showPassword ? 'text' : 'password'"
          placeholder="请输入密码"
          class="pl-10 pr-10"
          :disabled="loading"
          autocomplete="current-password"
        />
        <button
          type="button"
          class="absolute right-3 top-1/2 -translate-y-1/2 text-muted-foreground hover:text-foreground"
          @click="togglePassword"
        >
          <component :is="showPassword ? EyeOff : Eye" class="h-4 w-4" />
        </button>
      </div>
    </div>

    <!-- 错误提示 -->
    <div v-if="error" class="text-sm text-destructive">
      {{ error }}
    </div>

    <!-- 提交按钮 -->
    <Button
      type="submit"
      class="w-full"
      :disabled="!canSubmit"
    >
      <span v-if="loading">登录中...</span>
      <span v-else>登录</span>
    </Button>

    <!-- 忘记密码 -->
    <div class="text-center">
      <button
        type="button"
        class="text-sm text-primary hover:underline"
        @click="$emit('forgot-password')"
      >
        忘记密码？
      </button>
    </div>
  </form>
</template>
