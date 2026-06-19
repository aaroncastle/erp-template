<script setup lang="ts">
import { ref } from 'vue'
import { Copy, Check } from '@lucide/vue'
import Toast from './Toast.vue'

interface Props {
  value: string
  label?: string
  showLabel?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  label: '',
  showLabel: true,
})

const copied = ref(false)
const showToast = ref(false)

async function copyToClipboard() {
  try {
    await navigator.clipboard.writeText(props.value)
    copied.value = true
    showToast.value = true

    setTimeout(() => {
      copied.value = false
    }, 2000)

    setTimeout(() => {
      showToast.value = false
    }, 3000)
  } catch (err) {
    console.error('复制失败:', err)
  }
}
</script>

<template>
  <div class="inline-flex items-center gap-1.5">
    <span v-if="showLabel && label" class="text-xs text-muted-foreground">{{ label }}</span>
    <span class="font-mono text-sm font-medium text-foreground">{{ value }}</span>
    <button
      class="p-0.5 rounded hover:bg-muted transition-colors relative group"
      :title="copied ? '已复制' : '点击复制'"
      @click="copyToClipboard"
    >
      <Check v-if="copied" class="h-3.5 w-3.5 text-green-500" />
      <Copy v-else class="h-3.5 w-3.5 text-muted-foreground group-hover:text-foreground" />
    </button>

    <Toast
      v-model:open="showToast"
      message="已复制编号"
      type="success"
      position="top-right"
    />
  </div>
</template>
