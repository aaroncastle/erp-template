<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import ThemeToggle from '@/components/ThemeToggle.vue'
import { Card, CardContent } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Label } from '@/components/ui/label'
import { Checkbox } from '@/components/ui/checkbox'
import { Lock, Mail, Eye, EyeOff, LogIn } from '@lucide/vue'

const router = useRouter()
const employeeId = ref('')
const password = ref('')
const rememberMe = ref(false)
const showPassword = ref(false)

const isValid = computed(() => {
  return employeeId.value.trim() && password.value.trim()
})

function handleLogin() {
  if (!isValid.value) return
  // TODO: 实现登录逻辑
  console.log('登录:', { employeeId: employeeId.value, rememberMe: rememberMe.value })
}

function goToResetPassword() {
  router.push('/reset-password')
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center p-4 bg-background">
    <!-- 主题切换按钮 -->
    <div class="absolute top-4 right-4">
      <ThemeToggle />
    </div>

    <!-- 登录卡片 -->
    <Card class="w-full max-w-[400px]">
      <CardContent class="pt-6">
        <!-- 图标和标题 -->
        <div class="text-center mb-6">
          <div class="inline-flex items-center justify-center w-12 h-12 rounded-full bg-primary/10 mb-4">
            <Lock class="w-6 h-6 text-primary" />
          </div>
          <h1 class="text-2xl font-semibold text-foreground">欢迎回来</h1>
          <p class="text-sm text-muted-foreground mt-2">输入你的工号和密码登录账户</p>
        </div>

        <!-- 表单 -->
        <form @submit.prevent="handleLogin" class="space-y-4">
          <!-- 工号 -->
          <div class="space-y-2">
            <Label>工号</Label>
            <div class="relative">
              <Mail class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
              <Input
                v-model="employeeId"
                type="text"
                placeholder="请输入工号"
                class="pl-10"
              />
            </div>
          </div>

          <!-- 密码 -->
          <div class="space-y-2">
            <Label>密码</Label>
            <div class="relative">
              <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
              <Input
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                placeholder="请输入密码"
                class="pl-10 pr-10"
              />
              <button
                type="button"
                class="absolute right-3 top-1/2 -translate-y-1/2 text-muted-foreground hover:text-foreground"
                @click="showPassword = !showPassword"
              >
                <component :is="showPassword ? EyeOff : Eye" class="h-4 w-4" />
              </button>
            </div>
          </div>

          <!-- 记住我 和 忘记密码 -->
          <div class="flex items-center justify-between">
            <div class="flex items-center space-x-2">
              <Checkbox
                id="remember"
                :checked="rememberMe"
                @update:checked="rememberMe = $event"
              />
              <Label for="remember" class="text-sm font-normal cursor-pointer select-none">记住我</Label>
            </div>
            <a href="#" class="text-sm text-primary hover:underline" @click.prevent="goToResetPassword">忘记密码？</a>
          </div>

          <!-- 登录按钮 -->
          <Button type="submit" class="w-full" :disabled="!isValid">
            <LogIn class="mr-2 h-4 w-4" />
            登录
          </Button>
        </form>
      </CardContent>
    </Card>
  </div>
</template>
