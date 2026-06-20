<script setup lang="ts">
import { Inbox, FileX, SearchX, AlertCircle } from '@lucide/vue'
import { Button } from '@/components/ui/button'

export type EmptyType = 'default' | 'data' | 'search' | 'error'

interface Props {
  type?: EmptyType
  title?: string
  description?: string
  showAction?: boolean
  actionText?: string
}

const props = withDefaults(defineProps<Props>(), {
  type: 'default',
  title: '',
  description: '',
  showAction: false,
  actionText: '创建',
})

const emit = defineEmits<{
  (e: 'action'): void
}>()

const iconMap = {
  default: Inbox,
  data: FileX,
  search: SearchX,
  error: AlertCircle,
}

const defaultTitle = {
  default: '暂无内容',
  data: '暂无数据',
  search: '未找到结果',
  error: '出现错误',
}

const defaultDescription = {
  default: '当前没有任何内容',
  data: '当前没有任何数据',
  search: '未找到匹配的结果，请尝试其他搜索条件',
  error: '加载数据时出现错误',
}

const Icon = iconMap[props.type]
const title = props.title || defaultTitle[props.type]
const description = props.description || defaultDescription[props.type]
</script>

<template>
  <div class="flex flex-col items-center justify-center py-12 px-4 text-center">
    <component
      :is="Icon"
      class="h-16 w-16 text-muted-foreground mb-4"
    />
    <h3 class="text-lg font-medium text-foreground mb-2">{{ title }}</h3>
    <p class="text-sm text-muted-foreground max-w-md">{{ description }}</p>
    <Button
      v-if="showAction"
      class="mt-4"
      @click="emit('action')"
    >
      {{ actionText }}
    </Button>
  </div>
</template>
