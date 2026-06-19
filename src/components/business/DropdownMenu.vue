<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { ChevronDown } from '@lucide/vue'

export interface MenuItem {
  key: string
  label: string
  icon?: any
  disabled?: boolean
  danger?: boolean
  divider?: boolean
}

interface Props {
  items: MenuItem[]
  trigger?: 'click' | 'hover'
  align?: 'left' | 'right'
  width?: string
}

const props = withDefaults(defineProps<Props>(), {
  trigger: 'click',
  align: 'left',
  width: '200px',
})

const emit = defineEmits<{
  (e: 'select', item: MenuItem): void
}>()

const isOpen = ref(false)
const menuRef = ref<HTMLElement | null>(null)

const alignClasses = {
  left: 'left-0',
  right: 'right-0',
}

function toggle() {
  if (props.trigger === 'click') {
    isOpen.value = !isOpen.value
  }
}

function open() {
  if (props.trigger === 'hover') {
    isOpen.value = true
  }
}

function close() {
  isOpen.value = false
}

function selectItem(item: MenuItem) {
  if (item.disabled || item.divider) return
  emit('select', item)
  close()
}

// 点击外部关闭
function handleClickOutside(event: MouseEvent) {
  if (menuRef.value && !menuRef.value.contains(event.target as Node)) {
    close()
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<template>
  <div ref="menuRef" class="relative inline-block">
    <!-- 触发器 -->
    <div
      class="cursor-pointer"
      @click="toggle"
      @mouseenter="open"
      @mouseleave="close"
    >
      <slot />
    </div>

    <!-- 下拉菜单 -->
    <Transition
      enter-active-class="transition ease-out duration-100"
      enter-from-class="transform opacity-0 scale-95"
      enter-to-class="transform opacity-100 scale-100"
      leave-active-class="transition ease-in duration-75"
      leave-from-class="transform opacity-100 scale-100"
      leave-to-class="transform opacity-0 scale-95"
    >
      <div
        v-if="isOpen"
        class="absolute z-50 mt-1 rounded-md border border-border bg-background shadow-lg"
        :class="alignClasses[align]"
        :style="{ width }"
        @mouseenter="open"
        @mouseleave="close"
      >
        <div class="p-1">
          <template v-for="item in items" :key="item.key">
            <!-- 分隔线 -->
            <div
              v-if="item.divider"
              class="my-1 h-px bg-border"
            />
            <!-- 菜单项 -->
            <button
              v-else
              class="flex w-full items-center gap-2 rounded-sm px-2 py-1.5 text-sm transition-colors"
              :class="[
                item.disabled
                  ? 'text-muted-foreground cursor-not-allowed'
                  : item.danger
                    ? 'text-destructive hover:bg-destructive/10'
                    : 'text-foreground hover:bg-muted',
              ]"
              :disabled="item.disabled"
              @click="selectItem(item)"
            >
              <component
                v-if="item.icon"
                :is="item.icon"
                class="h-4 w-4"
              />
              <span>{{ item.label }}</span>
            </button>
          </template>
        </div>
      </div>
    </Transition>
  </div>
</template>
