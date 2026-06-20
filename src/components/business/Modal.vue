<script setup lang="ts">
import { X } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'

interface Props {
  open?: boolean
  title?: string
  width?: 'sm' | 'md' | 'lg' | 'xl' | 'full'
  showClose?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
  title: '',
  width: 'md',
  showClose: true,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'close'): void
}>()

const widthClasses = {
  sm: 'w-[400px]',
  md: 'w-[600px]',
  lg: 'w-[800px]',
  xl: 'w-[1000px]',
  full: 'w-[90vw]',
}

function handleClose() {
  emit('update:open', false)
  emit('close')
}
</script>

<template>
  <Teleport to="body">
    <div
      v-if="open"
      class="fixed inset-0 z-[200] bg-black/50 backdrop-blur-sm flex items-center justify-center"
      @click.self="handleClose"
    >
      <Card :class="[widthClasses[width], 'max-h-[90vh] flex flex-col']">
        <CardHeader v-if="title || showClose" class="flex-shrink-0">
          <div class="flex items-center justify-between">
            <CardTitle v-if="title">{{ title }}</CardTitle>
            <div class="flex items-center gap-2">
              <slot name="header-actions" />
              <Button
                v-if="showClose"
                variant="ghost"
                size="sm"
                @click="handleClose"
              >
                <X class="h-4 w-4" />
              </Button>
            </div>
          </div>
        </CardHeader>

        <CardContent class="flex-1 overflow-y-auto">
          <slot />
        </CardContent>

        <div v-if="$slots.footer" class="border-t border-border p-4 flex-shrink-0">
          <slot name="footer" />
        </div>
      </Card>
    </div>
  </Teleport>
</template>
