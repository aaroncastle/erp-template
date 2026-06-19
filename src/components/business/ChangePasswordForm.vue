<script setup lang="ts">
import { ref, computed } from 'vue'
import { Lock, Eye, EyeOff } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Label } from '@/components/ui/label'
import AlertBanner from './AlertBanner.vue'

interface Props {
  loading?: boolean
  error?: string
  requireOldPassword?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  loading: false,
  error: '',
  requireOldPassword: false,
})

const emit = defineEmits<{
  (e: 'submit', data: { oldPassword?: string; newPassword: string; confirmPassword: string }): void
  (e: 'cancel'): void
}>()

const oldPassword = ref('')
const newPassword = ref('')
const confirmPassword = ref('')
const showOldPassword = ref(false)
const showNewPassword = ref(false)
const showConfirmPassword = ref(false)

// 密码验证
const passwordErrors = computed(() => {
  const errors: string[] = []
  if (newPassword.value.length < 6) {
    errors.push('密码长度至少6位')
  }
  if (newPassword.value && !/[A-Za-z]/.test(newPassword.value)) {
    errors.push('密码必须包含字母')
  }
  if (newPassword.value && !/[0-9]/.test(newPassword.value)) {
    errors.push('密码必须包含数字')
  }
  return errors
})

const canSubmit = computed(() => {
  if (props.requireOldPassword && !oldPassword.value.trim()) return false
  if (!newPassword.value.trim()) return false
  if (newPassword.value !== confirmPassword.value) return false
  if (passwordErrors.value.length > 0) return false
  return !props.loading
})

function handleSubmit() {
  if (!canSubmit.value) return
  emit('submit', {
    oldPassword: props.requireOldPassword ? oldPassword.value : undefined,
    newPassword: newPassword.value,
    confirmPassword: confirmPassword.value,
  })
}
</script>

<template>
  <form @submit.prevent="handleSubmit" class="space-y-4">
    <!-- 提示信息 -->
    <AlertBanner
      type="info"
      message="首次登录需要修改密码，新密码不能与旧密码相同"
    />

    <!-- 旧密码 -->
    <div v-if="requireOldPassword" class="space-y-2">
      <Label for="oldPassword">旧密码</Label>
      <div class="relative">
        <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          id="oldPassword"
          v-model="oldPassword"
          :type="showOldPassword ? 'text' : 'password'"
          placeholder="请输入旧密码"
          class="pl-10 pr-10"
          :disabled="loading"
          autocomplete="current-password"
        />
        <button
          type="button"
          class="absolute right-3 top-1/2 -translate-y-1/2 text-muted-foreground hover:text-foreground"
          @click="showOldPassword = !showOldPassword"
        >
          <component :is="showOldPassword ? EyeOff : Eye" class="h-4 w-4" />
        </button>
      </div>
    </div>

    <!-- 新密码 -->
    <div class="space-y-2">
      <Label for="newPassword">新密码</Label>
      <div class="relative">
        <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          id="newPassword"
          v-model="newPassword"
          :type="showNewPassword ? 'text' : 'password'"
          placeholder="请输入新密码"
          class="pl-10 pr-10"
          :disabled="loading"
          autocomplete="new-password"
        />
        <button
          type="button"
          class="absolute right-3 top-1/2 -translate-y-1/2 text-muted-foreground hover:text-foreground"
          @click="showNewPassword = !showNewPassword"
        >
          <component :is="showNewPassword ? EyeOff : Eye" class="h-4 w-4" />
        </button>
      </div>
      <!-- 密码强度提示 -->
      <div v-if="passwordErrors.length > 0" class="text-xs text-destructive space-y-1">
        <p v-for="(error, index) in passwordErrors" :key="index">{{ error }}</p>
      </div>
    </div>

    <!-- 确认密码 -->
    <div class="space-y-2">
      <Label for="confirmPassword">确认新密码</Label>
      <div class="relative">
        <Lock class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          id="confirmPassword"
          v-model="confirmPassword"
          :type="showConfirmPassword ? 'text' : 'password'"
          placeholder="请再次输入新密码"
          class="pl-10 pr-10"
          :disabled="loading"
          autocomplete="new-password"
        />
        <button
          type="button"
          class="absolute right-3 top-1/2 -translate-y-1/2 text-muted-foreground hover:text-foreground"
          @click="showConfirmPassword = !showConfirmPassword"
        >
          <component :is="showConfirmPassword ? EyeOff : Eye" class="h-4 w-4" />
        </button>
      </div>
      <!-- 密码不匹配提示 -->
      <p
        v-if="confirmPassword && newPassword !== confirmPassword"
        class="text-xs text-destructive"
      >
        两次输入的密码不一致
      </p>
    </div>

    <!-- 错误提示 -->
    <div v-if="error" class="text-sm text-destructive">
      {{ error }}
    </div>

    <!-- 操作按钮 -->
    <div class="flex items-center gap-2">
      <Button
        v-if="!requireOldPassword"
        type="button"
        variant="outline"
        class="flex-1"
        @click="emit('cancel')"
      >
        取消
      </Button>
      <Button
        type="submit"
        class="flex-1"
        :disabled="!canSubmit"
      >
        <span v-if="loading">提交中...</span>
        <span v-else>确认修改</span>
      </Button>
    </div>
  </form>
</template>
