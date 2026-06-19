<script setup lang="ts">
import { Bell, Clock, FileText, AlertCircle, CheckCircle } from '@lucide/vue'

interface Props {
  id: string
  type: string
  title: string
  description: string
  time: string
  isRead?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  isRead: false,
})

const emit = defineEmits<{
  (e: 'click'): void
}>()

const typeIcons: Record<string, any> = {
  pending_invoice: FileText,
  modification_request: AlertCircle,
  storage_complete: CheckCircle,
  default: Bell,
}

const typeColors: Record<string, string> = {
  pending_invoice: 'text-blue-500',
  modification_request: 'text-yellow-500',
  storage_complete: 'text-green-500',
  default: 'text-muted-foreground',
}

const icon = typeIcons[props.type] || typeIcons.default
const color = typeColors[props.type] || typeColors.default
</script>

<template>
  <div
    class="rounded-lg border border-border p-3 cursor-pointer hover:bg-accent transition-colors"
    :class="{ 'opacity-60': isRead }"
    @click="emit('click')"
  >
    <div class="flex items-start gap-3">
      <!-- 未读标记 -->
      <div v-if="!isRead" class="mt-1.5 h-2 w-2 rounded-full bg-primary flex-shrink-0"></div>
      <div v-else class="mt-1.5 w-2 flex-shrink-0"></div>

      <!-- 图标 -->
      <component :is="icon" :class="['h-4 w-4 flex-shrink-0 mt-0.5', color]" />

      <!-- 内容 -->
      <div class="flex-1 min-w-0">
        <div class="flex items-center justify-between gap-2">
          <p class="text-sm font-medium text-foreground truncate">
            {{ title }}
          </p>
          <div class="flex items-center gap-1 text-xs text-muted-foreground flex-shrink-0">
            <Clock class="h-3 w-3" />
            {{ time }}
          </div>
        </div>
        <p class="text-xs text-muted-foreground mt-1 truncate">
          {{ description }}
        </p>
      </div>
    </div>
  </div>
</template>
