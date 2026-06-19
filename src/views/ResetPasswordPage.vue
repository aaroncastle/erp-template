<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import ThemeToggle from '@/components/ThemeToggle.vue'
import { Card, CardContent } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Label } from '@/components/ui/label'
import { Lock, Phone, Shield, ArrowLeft, CheckCircle } from '@lucide/vue'

const router = useRouter()

// 步骤：1-输入信息，2-验证验证码，3-设置新密码，4-完成
const step = ref(1)

const employeeId = ref('')
const phone = ref('')
const verificationCode = ref('')
const newPassword = ref('')
const confirmPassword = ref('')

// 倒计时
const countdown = ref(0)
let countdownTimer: ReturnType<typeof setInterval> | null = null

const isStep1Valid = computed(() => {
  return employeeId.value.trim() && phone.value.trim()
})

const isStep2Valid = computed(() => {
  return verificationCode.value.trim().length === 6
})

const isStep3Valid = computed(() => {
  return newPassword.value.trim() &&
         confirmPassword.value.trim() &&
         newPassword.value === confirmPassword.value
})

function sendVerificationCode() {
  if (!isStep1Valid.value) return
  // TODO: 调用 API 验证工号和手机号是否匹配，匹配则发送验证码
  console.log('发送验证码:', { employeeId: employeeId.value, phone: phone.value })

  // 开始倒计时
  countdown.value = 60
  countdownTimer = setInterval(() => {
    countdown.value--
    if (countdown.value <= 0) {
      clearInterval(countdownTimer!)
      countdownTimer = null
    }
  }, 1000)

  // 进入下一步
  step.value = 2
}

function verifyCode() {
  if (!isStep2Valid.value) return
  // TODO: 调用 API 验证验证码
  console.log('验证验证码:', verificationCode.value)

  // 进入下一步
  step.value = 3
}

function resetPassword() {
  if (!isStep3Valid.value) return
  // TODO: 调用 API 重置密码
  console.log('重置密码:', { employeeId: employeeId.value, newPassword: newPassword.value })

  // 进入完成步骤
  step.value = 4
}

function goBack() {
  if (step.value > 1) {
    step.value--
  } else {
    router.push('/login')
  }
}

function goLogin() {
  router.push('/login')
}

function resendCode() {
  if (countdown.value > 0) return
  sendVerificationCode()
}
</script>

<template>
  <div class="min-h-screen flex items-center justify-center p-4 bg-background">
    <!-- 主题切换按钮 -->
    <div class="absolute top-4 right-4">
      <ThemeToggle />
    </div>

    <!-- 重置密码卡片 -->
    <Card class="w-full max-w-[400px]">
      <CardContent class="pt-6">
        <!-- 步骤 1：输入工号和手机号 -->
        <div v-if="step === 1">
          <div class="text-center mb-6">
            <div class="inline-flex items-center justify-center w-12 h-12 rounded-full bg-primary/10 mb-4">
              <Phone class="w-6 h-6 text-primary" />
            </div>
            <h1 class="text-2xl font-semibold text-foreground">重置密码</h1>
            <p class="text-sm text-muted-foreground mt-2">输入工号和手机号验证身份</p>
          </div>

          <form @submit.prevent="sendVerificationCode" class="space-y-4">
            <!-- 工号 -->
            <div class="space-y-2">
              <Label>工号</Label>
              <div class="relative">
                <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
                <Input
                  v-model="employeeId"
                  type="text"
                  placeholder="请输入工号"
                  class="pl-10"
                />
              </div>
            </div>

            <!-- 手机号 -->
            <div class="space-y-2">
              <Label>手机号</Label>
              <div class="relative">
                <Phone class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
                <Input
                  v-model="phone"
                  type="tel"
                  placeholder="请输入手机号"
                  class="pl-10"
                />
              </div>
            </div>

            <!-- 发送验证码按钮 -->
            <Button type="submit" class="w-full" :disabled="!isStep1Valid">
              <Shield class="mr-2 h-4 w-4" />
              发送验证码
            </Button>

            <!-- 返回登录 -->
            <Button type="button" variant="outline" class="w-full" @click="goBack">
              <ArrowLeft class="mr-2 h-4 w-4" />
              返回登录
            </Button>
          </form>
        </div>

        <!-- 步骤 2：输入验证码 -->
        <div v-else-if="step === 2">
          <div class="text-center mb-6">
            <div class="inline-flex items-center justify-center w-12 h-12 rounded-full bg-primary/10 mb-4">
              <Shield class="w-6 h-6 text-primary" />
            </div>
            <h1 class="text-2xl font-semibold text-foreground">验证身份</h1>
            <p class="text-sm text-muted-foreground mt-2">
              验证码已发送至 {{ phone }}
            </p>
          </div>

          <form @submit.prevent="verifyCode" class="space-y-4">
            <!-- 验证码 -->
            <div class="space-y-2">
              <Label>验证码</Label>
              <Input
                v-model="verificationCode"
                type="text"
                placeholder="请输入 6 位验证码"
                maxlength="6"
                class="text-center text-lg tracking-widest"
              />
            </div>

            <!-- 重新发送 -->
            <div class="text-center text-sm">
              <button
                v-if="countdown > 0"
                type="button"
                class="text-muted-foreground cursor-not-allowed"
                disabled
              >
                {{ countdown }} 秒后重新发送
              </button>
              <button
                v-else
                type="button"
                class="text-primary hover:underline"
                @click="resendCode"
              >
                重新发送验证码
              </button>
            </div>

            <!-- 验证按钮 -->
            <Button type="submit" class="w-full" :disabled="!isStep2Valid">
              <Shield class="mr-2 h-4 w-4" />
              验证
            </Button>

            <!-- 返回 -->
            <Button type="button" variant="outline" class="w-full" @click="goBack">
              <ArrowLeft class="mr-2 h-4 w-4" />
              返回
            </Button>
          </form>
        </div>

        <!-- 步骤 3：设置新密码 -->
        <div v-else-if="step === 3">
          <div class="text-center mb-6">
            <div class="inline-flex items-center justify-center w-12 h-12 rounded-full bg-primary/10 mb-4">
              <Lock class="w-6 h-6 text-primary" />
            </div>
            <h1 class="text-2xl font-semibold text-foreground">设置新密码</h1>
            <p class="text-sm text-muted-foreground mt-2">请输入您的新密码</p>
          </div>

          <form @submit.prevent="resetPassword" class="space-y-4">
            <!-- 新密码 -->
            <div class="space-y-2">
              <Label>新密码</Label>
              <div class="relative">
                <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
                <Input
                  v-model="newPassword"
                  type="password"
                  placeholder="请输入新密码"
                  class="pl-10"
                />
              </div>
            </div>

            <!-- 确认密码 -->
            <div class="space-y-2">
              <Label>确认密码</Label>
              <div class="relative">
                <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
                <Input
                  v-model="confirmPassword"
                  type="password"
                  placeholder="请再次输入新密码"
                  class="pl-10"
                />
              </div>
              <p v-if="confirmPassword && newPassword !== confirmPassword" class="text-sm text-destructive">
                两次输入的密码不一致
              </p>
            </div>

            <!-- 重置按钮 -->
            <Button type="submit" class="w-full" :disabled="!isStep3Valid">
              <Lock class="mr-2 h-4 w-4" />
              重置密码
            </Button>

            <!-- 返回 -->
            <Button type="button" variant="outline" class="w-full" @click="goBack">
              <ArrowLeft class="mr-2 h-4 w-4" />
              返回
            </Button>
          </form>
        </div>

        <!-- 步骤 4：完成 -->
        <div v-else>
          <div class="text-center mb-6">
            <div class="inline-flex items-center justify-center w-12 h-12 rounded-full bg-green-500/10 mb-4">
              <CheckCircle class="w-6 h-6 text-green-500" />
            </div>
            <h1 class="text-2xl font-semibold text-foreground">重置成功</h1>
            <p class="text-sm text-muted-foreground mt-2">您的密码已成功重置</p>
          </div>

          <Button class="w-full" @click="goLogin">
            返回登录
          </Button>
        </div>
      </CardContent>
    </Card>
  </div>
</template>
