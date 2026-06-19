<script setup lang="ts">
import { computed, ref } from 'vue'
import { ChevronLeft, ChevronRight } from '@lucide/vue'
import { Button } from '@/components/ui/button'

interface Props {
  current: number
  total: number
  pageSize?: number
  showQuickJumper?: boolean
  showSizeChanger?: boolean
  pageSizeOptions?: number[]
}

const props = withDefaults(defineProps<Props>(), {
  pageSize: 10,
  showQuickJumper: false,
  showSizeChanger: false,
  pageSizeOptions: () => [10, 20, 50, 100],
})

const emit = defineEmits<{
  (e: 'update:current', value: number): void
  (e: 'update:pageSize', value: number): void
  (e: 'change', page: number, pageSize: number): void
}>()

// 总页数
const totalPages = computed(() => {
  return Math.ceil(props.total / props.pageSize)
})

// 显示分页范围
const pageRange = computed(() => {
  const start = (props.current - 1) * props.pageSize + 1
  const end = Math.min(props.current * props.pageSize, props.total)
  return `${start}-${end}`
})

// 计算显示的页码
const visiblePages = computed(() => {
  const pages: (number | string)[] = []
  const total = totalPages.value
  const current = props.current

  if (total <= 7) {
    // 总页数小于等于7，全部显示
    for (let i = 1; i <= total; i++) {
      pages.push(i)
    }
  } else {
    // 总页数大于7，显示省略号
    if (current <= 4) {
      // 当前页在前4页
      for (let i = 1; i <= 5; i++) {
        pages.push(i)
      }
      pages.push('...')
      pages.push(total)
    } else if (current >= total - 3) {
      // 当前页在后4页
      pages.push(1)
      pages.push('...')
      for (let i = total - 4; i <= total; i++) {
        pages.push(i)
      }
    } else {
      // 当前页在中间
      pages.push(1)
      pages.push('...')
      for (let i = current - 2; i <= current + 2; i++) {
        pages.push(i)
      }
      pages.push('...')
      pages.push(total)
    }
  }

  return pages
})

// 翻页
function goToPage(page: number | string) {
  if (typeof page === 'string') return
  if (page < 1 || page > totalPages.value) return
  if (page === props.current) return

  emit('update:current', page)
  emit('change', page, props.pageSize)
}

// 改变每页数量
function changePageSize(size: number) {
  emit('update:pageSize', size)
  emit('change', 1, size)
}

// 快速跳转
const jumpPage = ref('')
function jumpToPage() {
  const page = parseInt(jumpPage.value)
  if (!isNaN(page) && page >= 1 && page <= totalPages.value) {
    goToPage(page)
    jumpPage.value = ''
  }
}
</script>

<template>
  <div class="flex items-center justify-between">
    <!-- 分页范围 -->
    <div class="text-sm text-muted-foreground">
      显示 {{ pageRange }} / 共 {{ total }} 条
    </div>

    <!-- 分页控件 -->
    <div class="flex items-center gap-4">
      <!-- 每页数量选择 -->
      <div v-if="showSizeChanger" class="flex items-center gap-2">
        <span class="text-sm text-muted-foreground">每页</span>
        <select
          :value="pageSize"
          class="px-2 py-1 text-sm border border-border rounded-md bg-background"
          @change="changePageSize(Number(($event.target as HTMLSelectElement).value))"
        >
          <option v-for="size in pageSizeOptions" :key="size" :value="size">
            {{ size }}
          </option>
        </select>
        <span class="text-sm text-muted-foreground">条</span>
      </div>

      <!-- 页码 -->
      <div class="flex items-center gap-1">
        <Button
          variant="outline"
          size="sm"
          :disabled="current === 1"
          @click="goToPage(current - 1)"
        >
          <ChevronLeft class="h-4 w-4" />
        </Button>

        <template v-for="(page, index) in visiblePages" :key="index">
          <div
            v-if="page === '...'"
            class="px-2 text-muted-foreground"
          >
            ...
          </div>
          <div
            v-else
            class="px-3 py-1 text-sm rounded-md cursor-pointer transition-colors"
            :class="
              page === current
                ? 'bg-primary text-primary-foreground'
                : 'hover:bg-muted'
            "
            @click="goToPage(page)"
          >
            {{ page }}
          </div>
        </template>

        <Button
          variant="outline"
          size="sm"
          :disabled="current === totalPages"
          @click="goToPage(current + 1)"
        >
          <ChevronRight class="h-4 w-4" />
        </Button>
      </div>

      <!-- 快速跳转 -->
      <div v-if="showQuickJumper" class="flex items-center gap-2">
        <span class="text-sm text-muted-foreground">跳至</span>
        <input
          v-model="jumpPage"
          type="number"
          min="1"
          :max="totalPages"
          class="w-16 px-2 py-1 text-sm border border-border rounded-md bg-background"
          @keyup.enter="jumpToPage"
        />
        <span class="text-sm text-muted-foreground">页</span>
      </div>
    </div>
  </div>
</template>
