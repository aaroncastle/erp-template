<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'
import { cn } from '@/lib/utils'
import { Monitor, Sun, Moon } from '@lucide/vue'

type Theme = 'light' | 'dark' | 'system'

const theme = ref<Theme>('system')

const currentTheme = computed(() => {
  if (theme.value === 'system') {
    return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light'
  }
  return theme.value
})

function toggleTheme() {
  const themes: Theme[] = ['system', 'light', 'dark']
  const currentIndex = themes.indexOf(theme.value)
  theme.value = themes[(currentIndex + 1) % themes.length]
}

function applyTheme() {
  const root = document.documentElement
  if (currentTheme.value === 'dark') {
    root.classList.add('dark')
  } else {
    root.classList.remove('dark')
  }
}

onMounted(() => {
  const savedTheme = localStorage.getItem('theme') as Theme
  if (savedTheme) {
    theme.value = savedTheme
  }
  applyTheme()

  // 监听系统主题变化
  const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)')
  mediaQuery.addEventListener('change', () => {
    if (theme.value === 'system') {
      applyTheme()
    }
  })
})

watch(theme, (newTheme) => {
  localStorage.setItem('theme', newTheme)
  applyTheme()
})

const themeIcon = computed(() => {
  switch (theme.value) {
    case 'light':
      return Moon  // 当前是亮色，显示月亮（点击切到暗色）
    case 'dark':
      return Sun   // 当前是暗色，显示太阳（点击切到亮色）
    default:
      return Monitor
  }
})

const themeLabel = computed(() => {
  switch (theme.value) {
    case 'light':
      return '亮色主题'
    case 'dark':
      return '暗色主题'
    default:
      return '跟随系统'
  }
})
</script>

<template>
  <button
    class="inline-flex items-center justify-center rounded-md p-2 hover:bg-accent hover:text-accent-foreground transition-colors"
    :title="themeLabel"
    @click="toggleTheme"
  >
    <component :is="themeIcon" class="h-5 w-5" />
  </button>
</template>
