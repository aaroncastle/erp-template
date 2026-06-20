<script setup lang="ts">
import { Search, Plus } from '@lucide/vue'
import { ref } from 'vue'

interface FilterOption {
  label: string
  value: string
}

interface Filter {
  key: string
  label: string
  options: FilterOption[]
}

interface Props {
  filters?: Filter[]
  searchPlaceholder?: string
  showCreateButton?: boolean
  createButtonText?: string
}

withDefaults(defineProps<Props>(), {
  filters: () => [],
  searchPlaceholder: '搜索...',
  showCreateButton: false,
  createButtonText: '新建',
})

const emit = defineEmits<{
  (e: 'search', value: string): void
  (e: 'filter', key: string, value: string): void
  (e: 'create'): void
}>()

const searchValue = ref('')

function handleSearch() {
  emit('search', searchValue.value)
}

function handleFilterChange(key: string, value: string) {
  emit('filter', key, value)
}

function handleCreate() {
  emit('create')
}
</script>

<template>
  <div class="flex items-center gap-3 p-3 border-b border-border">
    <!-- 筛选下拉框 -->
    <div v-if="filters.length > 0" class="flex items-center gap-2">
      <select
        v-for="filter in filters"
        :key="filter.key"
        class="px-3 py-1.5 text-sm bg-muted border border-border rounded-md text-foreground focus:outline-none focus:ring-2 focus:ring-primary"
        @change="handleFilterChange(filter.key, ($event.target as HTMLSelectElement).value)"
      >
        <option value="">{{ filter.label }}</option>
        <option
          v-for="option in filter.options"
          :key="option.value"
          :value="option.value"
        >
          {{ option.label }}
        </option>
      </select>
    </div>

    <!-- 搜索框 -->
    <div class="flex-1 relative">
      <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
      <input
        v-model="searchValue"
        type="text"
        :placeholder="searchPlaceholder"
        class="w-full pl-9 pr-3 py-1.5 text-sm bg-muted border border-border rounded-md text-foreground placeholder:text-muted-foreground focus:outline-none focus:ring-2 focus:ring-primary"
        @input="handleSearch"
      />
    </div>

    <!-- 新建按钮 -->
    <button
      v-if="showCreateButton"
      class="flex items-center gap-1.5 px-3 py-1.5 text-sm font-medium bg-primary text-primary-foreground rounded-md hover:bg-primary/90 transition-colors"
      @click="handleCreate"
    >
      <Plus class="h-4 w-4" />
      <span>{{ createButtonText }}</span>
    </button>
  </div>
</template>
