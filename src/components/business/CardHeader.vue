<script setup lang="ts">
import { MoreHorizontal } from '@lucide/vue'
import StatusBadge from './StatusBadge.vue'
import DropdownMenu, { type MenuItem } from './DropdownMenu.vue'

interface Props {
  title?: string
  subtitle?: string
  status?: string
  menuItems?: MenuItem[]
}

defineProps<Props>()

const emit = defineEmits<{
  (e: 'menu-select', item: MenuItem): void
}>()

function handleMenuSelect(item: MenuItem) {
  emit('menu-select', item)
}
</script>

<template>
  <div class="flex items-center justify-between p-3 bg-muted border-b border-border">
    <div class="flex items-center gap-2">
      <div>
        <slot name="title">
          <p class="text-sm font-semibold text-foreground">{{ title }}</p>
          <p v-if="subtitle" class="text-xs text-muted-foreground mt-0.5">{{ subtitle }}</p>
        </slot>
      </div>
      <StatusBadge v-if="status" :status="status" />
    </div>
    <DropdownMenu
      v-if="menuItems && menuItems.length > 0"
      :items="menuItems"
      align="right"
      @select="handleMenuSelect"
    >
      <button class="p-1 rounded hover:bg-background transition-colors">
        <MoreHorizontal class="h-4 w-4 text-muted-foreground" />
      </button>
    </DropdownMenu>
  </div>
</template>
