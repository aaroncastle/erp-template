<script setup lang="ts">
import { Minus, Plus } from '@lucide/vue'
import { ref, computed, watch } from 'vue'

interface Props {
  modelValue: number | null
  min?: number
  max?: number
  step?: number
  precision?: number
  placeholder?: string
  disabled?: boolean
  prefix?: string
  suffix?: string
  controls?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  min: -Infinity,
  max: Infinity,
  step: 1,
  precision: 2,
  placeholder: '请输入',
  disabled: false,
  prefix: '',
  suffix: '',
  controls: true,
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: number | null): void
  (e: 'change', value: number | null): void
}>()

const inputValue = ref<string>(props.modelValue !== null ? formatNumber(props.modelValue) : '')

function formatNumber(value: number): string {
  return value.toFixed(props.precision)
}

function parseNumber(value: string): number | null {
  if (value === '' || value === '-') return null
  const num = parseFloat(value)
  if (isNaN(num)) return null
  return clamp(num)
}

function clamp(value: number): number {
  return Math.min(Math.max(value, props.min), props.max)
}

function handleInput(event: Event) {
  const target = event.target as HTMLInputElement
  inputValue.value = target.value
}

function handleBlur() {
  const value = parseNumber(inputValue.value)
  if (value !== null) {
    const formatted = formatNumber(value)
    inputValue.value = formatted
    emitValue(value)
  } else if (inputValue.value === '') {
    emitValue(null)
  } else {
    inputValue.value = props.modelValue !== null ? formatNumber(props.modelValue) : ''
  }
}

function handleKeydown(event: KeyboardEvent) {
  if (event.key === 'ArrowUp') {
    event.preventDefault()
    increment()
  } else if (event.key === 'ArrowDown') {
    event.preventDefault()
    decrement()
  }
}

function increment() {
  const current = props.modelValue ?? 0
  const newValue = clamp(current + props.step)
  inputValue.value = formatNumber(newValue)
  emitValue(newValue)
}

function decrement() {
  const current = props.modelValue ?? 0
  const newValue = clamp(current - props.step)
  inputValue.value = formatNumber(newValue)
  emitValue(newValue)
}

function emitValue(value: number | null) {
  emit('update:modelValue', value)
  emit('change', value)
}

watch(() => props.modelValue, (newValue) => {
  if (newValue !== null) {
    inputValue.value = formatNumber(newValue)
  } else {
    inputValue.value = ''
  }
})

const canIncrement = computed(() => {
  if (props.disabled) return false
  const current = props.modelValue ?? 0
  return current < props.max
})

const canDecrement = computed(() => {
  if (props.disabled) return false
  const current = props.modelValue ?? 0
  return current > props.min
})
</script>

<template>
  <div class="flex items-center gap-1">
    <!-- 前缀 -->
    <span v-if="prefix" class="text-sm text-muted-foreground">{{ prefix }}</span>

    <!-- 减少按钮 -->
    <button
      v-if="controls"
      type="button"
      class="flex items-center justify-center h-8 w-8 rounded-md border border-border bg-background hover:bg-muted disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
      :disabled="!canDecrement"
      @click="decrement"
    >
      <Minus class="h-4 w-4 text-muted-foreground" />
    </button>

    <!-- 输入框 -->
    <input
      type="text"
      :value="inputValue"
      :placeholder="placeholder"
      :disabled="disabled"
      class="flex-1 h-8 px-3 text-sm bg-background border border-border rounded-md text-foreground placeholder:text-muted-foreground focus:outline-none focus:ring-2 focus:ring-primary disabled:opacity-50 disabled:cursor-not-allowed text-center"
      @input="handleInput"
      @blur="handleBlur"
      @keydown="handleKeydown"
    />

    <!-- 增加按钮 -->
    <button
      v-if="controls"
      type="button"
      class="flex items-center justify-center h-8 w-8 rounded-md border border-border bg-background hover:bg-muted disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
      :disabled="!canIncrement"
      @click="increment"
    >
      <Plus class="h-4 w-4 text-muted-foreground" />
    </button>

    <!-- 后缀 -->
    <span v-if="suffix" class="text-sm text-muted-foreground">{{ suffix }}</span>
  </div>
</template>
