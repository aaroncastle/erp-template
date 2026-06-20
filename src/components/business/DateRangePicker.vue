<script setup lang="ts">
import { Calendar, ChevronLeft, ChevronRight } from '@lucide/vue'
import { ref, computed } from 'vue'

interface Props {
  modelValue: [string, string] | null
  placeholder?: string
  disabled?: boolean
  format?: string
}

const props = withDefaults(defineProps<Props>(), {
  placeholder: '选择日期范围',
  disabled: false,
  format: 'YYYY-MM-DD',
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: [string, string] | null): void
  (e: 'change', value: [string, string] | null): void
}>()

const isOpen = ref(false)
const currentDate = ref(new Date())
const startDate = ref<string>('')
const endDate = ref<string>('')
const selectingEnd = ref(false)

const months = ['一月', '二月', '三月', '四月', '五月', '六月', '七月', '八月', '九月', '十月', '十一月', '十二月']
const weekdays = ['日', '一', '二', '三', '四', '五', '六']

const displayValue = computed(() => {
  if (!props.modelValue) return ''
  const [start, end] = props.modelValue
  if (!start || !end) return ''
  return `${start} ~ ${end}`
})

const calendarDays = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  const firstDay = new Date(year, month, 1)
  const lastDay = new Date(year, month + 1, 0)
  const days: Array<{ date: Date; isCurrentMonth: boolean; isToday: boolean; isSelected: boolean; isInRange: boolean }> = []

  // 填充上月空白
  const firstDayOfWeek = firstDay.getDay()
  for (let i = firstDayOfWeek - 1; i >= 0; i--) {
    const date = new Date(year, month, -i)
    days.push({
      date,
      isCurrentMonth: false,
      isToday: false,
      isSelected: false,
      isInRange: false,
    })
  }

  // 本月日期
  const today = new Date()
  today.setHours(0, 0, 0, 0)

  for (let day = 1; day <= lastDay.getDate(); day++) {
    const date = new Date(year, month, day)
    const dateStr = formatDate(date)
    const isSelected = dateStr === startDate.value || dateStr === endDate.value
    const isInRange = startDate.value && endDate.value && dateStr > startDate.value && dateStr < endDate.value

    days.push({
      date,
      isCurrentMonth: true,
      isToday: date.getTime() === today.getTime(),
      isSelected,
      isInRange,
    })
  }

  // 填充下月空白
  const remaining = 42 - days.length
  for (let i = 1; i <= remaining; i++) {
    const date = new Date(year, month + 1, i)
    days.push({
      date,
      isCurrentMonth: false,
      isToday: false,
      isSelected: false,
      isInRange: false,
    })
  }

  return days
})

function formatDate(date: Date): string {
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  return `${year}-${month}-${day}`
}

function toggleCalendar() {
  if (props.disabled) return
  isOpen.value = !isOpen.value
  if (isOpen.value) {
    startDate.value = props.modelValue?.[0] || ''
    endDate.value = props.modelValue?.[1] || ''
    selectingEnd.value = false
  }
}

function selectDay(day: { date: Date; isCurrentMonth: boolean }) {
  if (!day.isCurrentMonth) return

  const dateStr = formatDate(day.date)

  if (!selectingEnd.value || !startDate.value) {
    startDate.value = dateStr
    endDate.value = ''
    selectingEnd.value = true
  } else {
    if (dateStr < startDate.value) {
      endDate.value = startDate.value
      startDate.value = dateStr
    } else {
      endDate.value = dateStr
    }
    selectingEnd.value = false

    // 提交值
    const value: [string, string] = [startDate.value, endDate.value]
    emit('update:modelValue', value)
    emit('change', value)
    isOpen.value = false
  }
}

function prevMonth() {
  const date = new Date(currentDate.value)
  date.setMonth(date.getMonth() - 1)
  currentDate.value = date
}

function nextMonth() {
  const date = new Date(currentDate.value)
  date.setMonth(date.getMonth() + 1)
  currentDate.value = date
}

function clearSelection() {
  startDate.value = ''
  endDate.value = ''
  selectingEnd.value = false
  emit('update:modelValue', null)
  emit('change', null)
  isOpen.value = false
}

function handleKeydown(event: KeyboardEvent) {
  if (event.key === 'Escape') {
    isOpen.value = false
  }
}
</script>

<template>
  <div class="relative" @keydown="handleKeydown">
    <!-- 输入框 -->
    <div
      class="flex items-center h-9 px-3 bg-background border border-border rounded-md cursor-pointer hover:border-primary transition-colors"
      :class="{ 'border-primary': isOpen, 'opacity-50 cursor-not-allowed': disabled }"
      @click="toggleCalendar"
    >
      <Calendar class="h-4 w-4 text-muted-foreground mr-2" />
      <span v-if="displayValue" class="flex-1 text-sm text-foreground">{{ displayValue }}</span>
      <span v-else class="flex-1 text-sm text-muted-foreground">{{ placeholder }}</span>
    </div>

    <!-- 日历面板 -->
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
        class="absolute z-50 mt-1 p-3 bg-background border border-border rounded-lg shadow-lg"
      >
        <!-- 月份导航 -->
        <div class="flex items-center justify-between mb-3">
          <button
            type="button"
            class="p-1 rounded hover:bg-muted transition-colors"
            @click="prevMonth"
          >
            <ChevronLeft class="h-4 w-4 text-muted-foreground" />
          </button>
          <span class="text-sm font-medium text-foreground">
            {{ currentDate.getFullYear() }}年 {{ months[currentDate.getMonth()] }}
          </span>
          <button
            type="button"
            class="p-1 rounded hover:bg-muted transition-colors"
            @click="nextMonth"
          >
            <ChevronRight class="h-4 w-4 text-muted-foreground" />
          </button>
        </div>

        <!-- 星期标题 -->
        <div class="grid grid-cols-7 gap-1 mb-2">
          <div
            v-for="weekday in weekdays"
            :key="weekday"
            class="text-center text-xs text-muted-foreground py-1"
          >
            {{ weekday }}
          </div>
        </div>

        <!-- 日期网格 -->
        <div class="grid grid-cols-7 gap-1">
          <button
            v-for="(day, index) in calendarDays"
            :key="index"
            type="button"
            class="h-8 w-8 text-xs rounded transition-colors"
            :class="[
              day.isCurrentMonth ? 'text-foreground' : 'text-muted-foreground',
              day.isToday && !day.isSelected ? 'border border-primary text-primary' : '',
              day.isSelected ? 'bg-primary text-primary-foreground' : '',
              day.isInRange ? 'bg-primary/10' : '',
              !day.isCurrentMonth ? 'cursor-default' : 'hover:bg-muted cursor-pointer',
            ]"
            :disabled="!day.isCurrentMonth"
            @click="selectDay(day)"
          >
            {{ day.date.getDate() }}
          </button>
        </div>

        <!-- 操作按钮 -->
        <div class="flex items-center justify-between mt-3 pt-3 border-t border-border">
          <button
            type="button"
            class="text-xs text-muted-foreground hover:text-foreground transition-colors"
            @click="clearSelection"
          >
            清空
          </button>
          <div class="flex items-center gap-2 text-xs text-muted-foreground">
            <span v-if="startDate">开始: {{ startDate }}</span>
            <span v-if="endDate">结束: {{ endDate }}</span>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>
