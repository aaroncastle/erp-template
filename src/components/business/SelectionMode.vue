<script setup lang="ts">
import { CheckSquare, Square, Trash2, FileText, Download } from '@lucide/vue'
import { ref, computed } from 'vue'

interface SelectableItem {
  id: string
  [key: string]: any
}

interface Props {
  items: SelectableItem[]
  selectable?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  selectable: true,
})

const emit = defineEmits<{
  (e: 'selection-change', selectedIds: string[]): void
  (e: 'batch-delete', selectedIds: string[]): void
  (e: 'batch-invoice', selectedIds: string[]): void
  (e: 'batch-export', selectedIds: string[]): void
}>()

const selectedIds = ref<Set<string>>(new Set())

const isAllSelected = computed(() => {
  return props.items.length > 0 && selectedIds.value.size === props.items.length
})

const isIndeterminate = computed(() => {
  return selectedIds.value.size > 0 && selectedIds.value.size < props.items.length
})

const selectedCount = computed(() => selectedIds.value.size)

function toggleSelectAll() {
  if (isAllSelected.value) {
    selectedIds.value.clear()
  } else {
    props.items.forEach(item => {
      selectedIds.value.add(item.id)
    })
  }
  emitSelectionChange()
}

function toggleSelect(item: SelectableItem) {
  if (selectedIds.value.has(item.id)) {
    selectedIds.value.delete(item.id)
  } else {
    selectedIds.value.add(item.id)
  }
  emitSelectionChange()
}

function isSelected(id: string): boolean {
  return selectedIds.value.has(id)
}

function clearSelection() {
  selectedIds.value.clear()
  emitSelectionChange()
}

function emitSelectionChange() {
  emit('selection-change', Array.from(selectedIds.value))
}

function handleBatchDelete() {
  emit('batch-delete', Array.from(selectedIds.value))
  clearSelection()
}

function handleBatchInvoice() {
  emit('batch-invoice', Array.from(selectedIds.value))
}

function handleBatchExport() {
  emit('batch-export', Array.from(selectedIds.value))
}
</script>

<template>
  <div v-if="selectable" class="space-y-3">
    <!-- 选择工具栏 -->
    <div class="flex items-center justify-between p-3 rounded-lg border border-border bg-muted/20">
      <div class="flex items-center gap-3">
        <button
          class="inline-flex items-center justify-center rounded-md h-8 w-8 hover:bg-accent transition-colors"
          :class="isAllSelected ? 'text-primary' : 'text-muted-foreground'"
          @click="toggleSelectAll"
        >
          <CheckSquare v-if="isAllSelected" class="h-5 w-5" />
          <Square v-else class="h-5 w-5" />
        </button>
        <span class="text-sm text-muted-foreground">
          已选择 <span class="font-medium text-foreground">{{ selectedCount }}</span> 项
        </span>
      </div>

      <!-- 批量操作按钮 -->
      <div v-if="selectedCount > 0" class="flex items-center gap-2">
        <button
          class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
          @click="handleBatchInvoice"
        >
          <FileText class="h-3.5 w-3.5" />
          联合开票
        </button>
        <button
          class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
          @click="handleBatchExport"
        >
          <Download class="h-3.5 w-3.5" />
          导出
        </button>
        <button
          class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md bg-destructive text-destructive-foreground hover:bg-destructive/90 transition-colors"
          @click="handleBatchDelete"
        >
          <Trash2 class="h-3.5 w-3.5" />
          删除
        </button>
        <button
          class="px-3 py-1.5 text-xs font-medium rounded-md text-muted-foreground hover:text-foreground transition-colors"
          @click="clearSelection"
        >
          取消选择
        </button>
      </div>
    </div>

    <!-- 选择项插槽 -->
    <slot :selected-ids="Array.from(selectedIds)" :is-selected="isSelected" :toggle-select="toggleSelect" />
  </div>

  <!-- 非选择模式，直接渲染插槽 -->
  <slot v-else :selected-ids="[]" :is-selected="() => false" :toggle-select="() => {}" />
</template>
