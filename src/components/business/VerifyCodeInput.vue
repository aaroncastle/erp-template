<script setup lang="ts">
import { ref, computed } from 'vue'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'

interface Props {
  length?: number
  cooldown?: number
  disabled?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  length: 6,
  cooldown: 60,
  disabled: false,
})

const emit = defineEmits<{
  (e: 'complete', code: string): void
  (e: 'send'): void
}>()

const code = ref('')
const countdown = ref(0)
const isSending = ref(false)

// 是否完成输入
const isComplete = computed(() => code.value.length === props.length)

// 是否可以发送
const canSend = computed(() => countdown.value === 0 && !isSending.value && !props.disabled)

// 发送按钮文本
const sendButtonText = computed(() => {
  if (isSending.value) return '发送中...'
  if (countdown.value > 0) return `${countdown.value}s`
  return '发送验证码'
})

// 输入处理
function handleInput(event: Event) {
  const target = event.target as HTMLInputElement
  // 只保留数字
  code.value = target.value.replace(/\D/g, '').slice(0, props.length)

  if (isComplete.value) {
    emit('complete', code.value)
  }
}

// 发送验证码
async function handleSend() {
  if (!canSend.value) return

  isSending.value = true
  emit('send')

  // 模拟发送
  setTimeout(() => {
    isSending.value = false
    startCountdown()
  }, 1000)
}

// 开始倒计时
function startCountdown() {
  countdown.value = props.cooldown
  const timer = setInterval(() => {
    countdown.value--
    if (countdown.value <= 0) {
      clearInterval(timer)
    }
  }, 1000)
}

// 清空
function clear() {
  code.value = ''
}

// 暴露方法
defineExpose({
  clear,
})
</script>

<template>
  <div class="space-y-3">
    <div class="flex items-center gap-3">
      <Input
        v-model="code"
        type="text"
        :placeholder="`请输入${length}位验证码`"
        :maxlength="length"
        :disabled="disabled"
        class="flex-1 text-center text-lg tracking-widest"
        @input="handleInput"
      />
      <Button
        variant="outline"
        :disabled="!canSend"
        class="min-w-[120px]"
        @click="handleSend"
      >
        {{ sendButtonText }}
      </Button>
    </div>

    <!-- 进度指示 -->
    <div class="flex items-center gap-1">
      <div
        v-for="i in length"
        :key="i"
        class="flex-1 h-1 rounded-full transition-colors"
        :class="code.length >= i ? 'bg-primary' : 'bg-muted'"
      />
    </div>
  </div>
</template>
