<script setup lang="ts">
interface Props {
  title?: string
  showHeader?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  title: '详情',
  showHeader: true,
})
</script>

<template>
  <div class="h-full flex flex-col border border-border rounded-lg bg-background">
    <!-- 头部 -->
    <div
      v-if="showHeader"
      class="flex items-center justify-between px-4 py-3 border-b border-border"
    >
      <h3 class="text-sm font-semibold text-foreground">{{ title }}</h3>
      <slot name="header-actions" />
    </div>

    <!-- 内容 -->
    <div class="flex-1 overflow-y-auto p-4">
      <slot />
    </div>

    <!-- 底部操作栏 -->
    <div
      v-if="$slots.footer"
      class="px-4 py-3 border-t border-border"
    >
      <slot name="footer" />
    </div>
  </div>
</template>
