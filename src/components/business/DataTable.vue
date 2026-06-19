<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { ChevronUp, ChevronDown, ChevronsUpDown, ChevronLeft, ChevronRight } from '@lucide/vue'
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Checkbox } from '@/components/ui/checkbox'

export interface Column<T> {
  key: keyof T
  label: string
  sortable?: boolean
  width?: string
  align?: 'left' | 'center' | 'right'
  render?: (value: any, row: T) => any
}

export interface SortConfig {
  key: string
  direction: 'asc' | 'desc'
}

interface Props<T> {
  columns: Column<T>[]
  data: T[]
  loading?: boolean
  selectable?: boolean
  pageSize?: number
  searchPlaceholder?: string
}

const props = withDefaults(defineProps<Props<any>>(), {
  loading: false,
  selectable: false,
  pageSize: 10,
  searchPlaceholder: '搜索...',
})

const emit = defineEmits<{
  (e: 'select', rows: any[]): void
  (e: 'row-click', row: any): void
}>()

// 搜索
const searchQuery = ref('')

// 排序
const sortConfig = ref<SortConfig | null>(null)

// 选择
const selectedRows = ref<any[]>([])

// 分页
const currentPage = ref(1)

// 计算过滤后的数据
const filteredData = computed(() => {
  let data = [...props.data]

  // 搜索过滤
  if (searchQuery.value.trim()) {
    const query = searchQuery.value.toLowerCase()
    data = data.filter(row => {
      return Object.values(row).some(value => {
        return String(value).toLowerCase().includes(query)
      })
    })
  }

  // 排序
  if (sortConfig.value) {
    const { key, direction } = sortConfig.value
    data.sort((a, b) => {
      const aVal = a[key]
      const bVal = b[key]

      if (aVal === bVal) return 0

      const compare = aVal > bVal ? 1 : -1
      return direction === 'asc' ? compare : -compare
    })
  }

  return data
})

// 计算分页后的数据
const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * props.pageSize
  const end = start + props.pageSize
  return filteredData.value.slice(start, end)
})

// 总页数
const totalPages = computed(() => {
  return Math.ceil(filteredData.value.length / props.pageSize)
})

// 显示分页范围
const pageRange = computed(() => {
  const start = (currentPage.value - 1) * props.pageSize + 1
  const end = Math.min(currentPage.value * props.pageSize, filteredData.value.length)
  return `${start}-${end} / ${filteredData.value.length}`
})

// 排序切换
function toggleSort(key: string) {
  if (!sortConfig.value || sortConfig.value.key !== key) {
    sortConfig.value = { key, direction: 'asc' }
  } else if (sortConfig.value.direction === 'asc') {
    sortConfig.value.direction = 'desc'
  } else {
    sortConfig.value = null
  }
}

// 全选/取消全选
function toggleSelectAll() {
  if (selectedRows.value.length === paginatedData.value.length) {
    selectedRows.value = []
  } else {
    selectedRows.value = [...paginatedData.value]
  }
  emit('select', selectedRows.value)
}

// 选择单行
function toggleSelectRow(row: any) {
  const index = selectedRows.value.findIndex(r => r === row)
  if (index >= 0) {
    selectedRows.value.splice(index, 1)
  } else {
    selectedRows.value.push(row)
  }
  emit('select', selectedRows.value)
}

// 是否全选
const isAllSelected = computed(() => {
  return paginatedData.value.length > 0 && selectedRows.value.length === paginatedData.value.length
})

// 翻页
function goToPage(page: number) {
  currentPage.value = page
}

// 监听数据变化，重置页码
watch(() => props.data, () => {
  currentPage.value = 1
  selectedRows.value = []
})
</script>

<template>
  <div class="space-y-4">
    <!-- 搜索栏 -->
    <div class="relative">
      <Input
        v-model="searchQuery"
        :placeholder="searchPlaceholder"
        class="pl-10"
      />
      <div class="absolute left-3 top-1/2 -translate-y-1/2 text-muted-foreground">
        <svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
      </div>
    </div>

    <!-- 表格 -->
    <div class="border border-border rounded-lg overflow-hidden">
      <Table>
        <TableHeader>
          <TableRow>
            <TableHead v-if="selectable" class="w-[50px]">
              <Checkbox
                :checked="isAllSelected"
                @update:checked="toggleSelectAll"
              />
            </TableHead>
            <TableHead
              v-for="column in columns"
              :key="String(column.key)"
              :style="{ width: column.width, textAlign: column.align || 'left' }"
            >
              <div
                class="flex items-center gap-1"
                :class="column.sortable ? 'cursor-pointer hover:text-foreground' : ''"
                @click="column.sortable && toggleSort(String(column.key))"
              >
                <span>{{ column.label }}</span>
                <component
                  v-if="column.sortable"
                  :is="
                    sortConfig?.key === String(column.key)
                      ? sortConfig.direction === 'asc'
                        ? ChevronUp
                        : ChevronDown
                      : ChevronsUpDown
                  "
                  class="h-4 w-4"
                  :class="sortConfig?.key === String(column.key) ? 'text-foreground' : 'text-muted-foreground'"
                />
              </div>
            </TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          <template v-if="loading">
            <TableRow>
              <TableCell :colspan="columns.length + (selectable ? 1 : 0)" class="text-center py-8">
                <div class="flex items-center justify-center gap-2 text-muted-foreground">
                  <div class="h-4 w-4 animate-spin rounded-full border-2 border-primary border-t-transparent" />
                  <span>加载中...</span>
                </div>
              </TableCell>
            </TableRow>
          </template>
          <template v-else-if="paginatedData.length === 0">
            <TableRow>
              <TableCell :colspan="columns.length + (selectable ? 1 : 0)" class="text-center py-8 text-muted-foreground">
                暂无数据
              </TableCell>
            </TableRow>
          </template>
          <template v-else>
            <TableRow
              v-for="row in paginatedData"
              :key="row.id || JSON.stringify(row)"
              class="cursor-pointer hover:bg-muted/50"
              @click="emit('row-click', row)"
            >
              <TableCell v-if="selectable">
                <Checkbox
                  :checked="selectedRows.includes(row)"
                  @update:checked="toggleSelectRow(row)"
                  @click.stop
                />
              </TableCell>
              <TableCell
                v-for="column in columns"
                :key="String(column.key)"
                :style="{ textAlign: column.align || 'left' }"
              >
                <component
                  :is="column.render ? column.render(row[column.key], row) : 'span'"
                  v-if="column.render"
                />
                <span v-else>{{ row[column.key] }}</span>
              </TableCell>
            </TableRow>
          </template>
        </TableBody>
      </Table>
    </div>

    <!-- 分页 -->
    <div v-if="filteredData.length > pageSize" class="flex items-center justify-between">
      <div class="text-sm text-muted-foreground">
        {{ pageRange }}
      </div>
      <div class="flex items-center gap-1">
        <Button
          variant="outline"
          size="sm"
          :disabled="currentPage === 1"
          @click="goToPage(currentPage - 1)"
        >
          <ChevronLeft class="h-4 w-4" />
        </Button>
        <div
          v-for="page in totalPages"
          :key="page"
          class="px-3 py-1 text-sm rounded-md cursor-pointer transition-colors"
          :class="
            page === currentPage
              ? 'bg-primary text-primary-foreground'
              : 'hover:bg-muted'
          "
          @click="goToPage(page)"
        >
          {{ page }}
        </div>
        <Button
          variant="outline"
          size="sm"
          :disabled="currentPage === totalPages"
          @click="goToPage(currentPage + 1)"
        >
          <ChevronRight class="h-4 w-4" />
        </Button>
      </div>
    </div>
  </div>
</template>
