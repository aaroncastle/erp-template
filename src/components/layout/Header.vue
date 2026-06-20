<script setup lang="ts">
import { computed } from 'vue'
import ThemeToggle from '@/components/ThemeToggle.vue'
import { User, ChevronDown } from '@lucide/vue'

interface Props {
  companyName?: string
  departmentName?: string
}

const props = withDefaults(defineProps<Props>(), {
  companyName: '洛阳能源密封件 ERP',
  departmentName: '销售一部',
})

const emit = defineEmits<{
  (e: 'user-menu-click'): void
}>()

const initials = computed(() => {
  return props.departmentName.split('').map(char => char[0]).join('').slice(0, 2)
})
</script>

<template>
  <header class="sticky top-0 z-50 w-full border-b border-border bg-background/95 backdrop-blur supports-[backdrop-filter]:bg-background/60">
    <div class="flex h-14 items-center justify-between px-4">
      <!-- 左侧：Logo + 公司名 -->
      <div class="flex items-center gap-3">
        <div class="flex h-8 w-8 items-center justify-center rounded-lg bg-primary/10">
          <div class="h-4 w-4 rounded bg-primary"></div>
        </div>
        <h1 class="text-base font-semibold text-foreground">
          {{ companyName }}
        </h1>
      </div>

      <!-- 中间：部门名称 -->
      <div class="flex items-center gap-2 rounded-md bg-muted px-3 py-1.5">
        <div class="flex h-5 w-5 items-center justify-center rounded bg-primary/20 text-xs font-medium text-primary">
          {{ initials }}
        </div>
        <span class="text-sm font-medium text-foreground">
          {{ departmentName }}
        </span>
      </div>

      <!-- 右侧：主题切换 + 用户菜单 -->
      <div class="flex items-center gap-2">
        <ThemeToggle />
        <button
          class="inline-flex items-center justify-center rounded-md p-2 hover:bg-accent hover:text-accent-foreground transition-colors"
          @click="emit('user-menu-click')"
        >
          <User class="h-5 w-5" />
          <ChevronDown class="h-4 w-4 ml-1" />
        </button>
      </div>
    </div>
  </header>
</template>
