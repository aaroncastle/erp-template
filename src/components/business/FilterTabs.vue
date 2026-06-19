<script setup lang="ts">
interface Tab {
  key: string
  label: string
  count?: number
}

interface Props {
  tabs: Tab[]
  activeTab: string
}

defineProps<Props>()

const emit = defineEmits<{
  (e: 'update:activeTab', tab: string): void
}>()

function selectTab(tabKey: string) {
  emit('update:activeTab', tabKey)
}
</script>

<template>
  <div class="flex items-center gap-1 border-b border-border">
    <button
      v-for="tab in tabs"
      :key="tab.key"
      class="relative px-4 py-2 text-sm font-medium transition-colors"
      :class="[
        activeTab === tab.key
          ? 'text-primary'
          : 'text-muted-foreground hover:text-foreground'
      ]"
      @click="selectTab(tab.key)"
    >
      <span>{{ tab.label }}</span>
      <span v-if="tab.count !== undefined" class="ml-1 text-xs">
        ({{ tab.count }})
      </span>
      <div
        v-if="activeTab === tab.key"
        class="absolute bottom-0 left-0 right-0 h-0.5 bg-primary"
      />
    </button>
  </div>
</template>
