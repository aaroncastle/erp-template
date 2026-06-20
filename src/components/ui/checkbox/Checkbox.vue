<script setup lang="ts">
import { type HTMLAttributes, computed } from 'vue'
import { cn } from '@/lib/utils'

const props = defineProps<{
  id?: string
  class?: HTMLAttributes['class']
  checked?: boolean
}>()

const emit = defineEmits<{
  (e: 'update:checked', value: boolean): void
}>()

const classes = computed(() =>
  cn(
    'peer h-4 w-4 shrink-0 rounded-sm border border-primary ring-offset-background focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50 data-[state=checked]:bg-primary data-[state=checked]:text-primary-foreground',
    props.class,
  ),
)

function toggle() {
  emit('update:checked', !props.checked)
}
</script>

<template>
  <button
    :id="id"
    type="button"
    role="checkbox"
    :aria-checked="checked"
    :data-state="checked ? 'checked' : 'unchecked'"
    :class="classes"
    @click="toggle"
  >
    <span v-if="checked" class="flex items-center justify-center text-current">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" class="h-3 w-3">
        <polyline points="20 6 9 17 4 12" />
      </svg>
    </span>
  </button>
</template>
