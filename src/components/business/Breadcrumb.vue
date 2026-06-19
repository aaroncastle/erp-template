<script setup lang="ts">
import { ChevronRight } from '@lucide/vue'

interface BreadcrumbItem {
  label: string
  icon?: any
  onClick?: () => void
}

interface Props {
  items: BreadcrumbItem[]
}

const props = defineProps<Props>()
</script>

<template>
  <nav class="flex items-center gap-1.5 text-sm">
    <template v-for="(item, index) in items" :key="index">
      <!-- 分隔符 -->
      <ChevronRight
        v-if="index > 0"
        class="h-4 w-4 text-muted-foreground flex-shrink-0"
      />

      <!-- 面包屑项 -->
      <button
        v-if="item.onClick && index < items.length - 1"
        class="inline-flex items-center gap-1 text-muted-foreground hover:text-foreground transition-colors"
        @click="item.onClick"
      >
        <component v-if="item.icon" :is="item.icon" class="h-4 w-4" />
        <span>{{ item.label }}</span>
      </button>

      <!-- 当前页（不可点击） -->
      <span
        v-else
        class="inline-flex items-center gap-1 text-foreground font-medium"
      >
        <component v-if="item.icon" :is="item.icon" class="h-4 w-4" />
        <span>{{ item.label }}</span>
      </span>
    </template>
  </nav>
</template>
