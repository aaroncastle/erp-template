<script setup lang="ts">
import { X } from '@lucide/vue'
import { ref, watch, onMounted, onUnmounted, nextTick } from 'vue'

interface Props {
  open?: boolean
  title?: string
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
  title: '详情',
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'close'): void
}>()

const isVisible = ref(props.open)

watch(() => props.open, (value) => {
  isVisible.value = value
})

function close() {
  isVisible.value = false
  emit('update:open', false)
  emit('close')
}

function handleEscape(e: KeyboardEvent) {
  if (e.key === 'Escape' && isVisible.value) {
    close()
  }
}

onMounted(() => {
  document.addEventListener('keydown', handleEscape)
})

onUnmounted(() => {
  document.removeEventListener('keydown', handleEscape)
})
</script>

<template>
  <!-- 遮罩层 -->
  <Teleport to="body">
    <div
      v-if="isVisible"
      class="fixed inset-0 z-[200] bg-black/50 backdrop-blur-sm"
      @click="close"
    />
  </Teleport>

  <!-- 抽屉面板 -->
  <Teleport to="body">
    <div
      v-if="isVisible"
      class="fixed top-0 right-0 bottom-0 z-[201] w-[80%] bg-background border-l border-border shadow-xl flex flex-col"
    >
      <!-- 抽屉头部 -->
      <div class="flex items-center justify-between p-4 border-b border-border">
        <h2 class="text-lg font-semibold text-foreground">{{ title }}</h2>
        <button
          class="inline-flex items-center justify-center rounded-md h-8 w-8 text-muted-foreground hover:text-foreground hover:bg-accent transition-colors"
          @click="close"
        >
          <X class="h-5 w-5" />
        </button>
      </div>

      <!-- 抽屉内容（左右分栏） -->
      <div class="flex-1 grid grid-cols-[60%_40%] min-h-0">
        <!-- 左侧：列表区域（可滚动） -->
        <div class="border-r border-border overflow-y-auto p-4">
          <slot name="list" />
        </div>

        <!-- 右侧：详情区域（可滚动） -->
        <div class="overflow-y-auto p-4">
          <slot name="detail" />
        </div>
      </div>

      <!-- 抽屉底部操作栏 -->
      <div class="border-t border-border p-4 flex items-center justify-end gap-2">
        <slot name="actions" />
      </div>
    </div>
  </Teleport>
</template>
