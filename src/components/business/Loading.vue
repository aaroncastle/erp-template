<script setup lang="ts">
interface Props {
  loading?: boolean
  text?: string
  size?: 'sm' | 'md' | 'lg'
  fullscreen?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  loading: true,
  text: '加载中...',
  size: 'md',
  fullscreen: false,
})

const sizeClasses = {
  sm: 'h-4 w-4',
  md: 'h-8 w-8',
  lg: 'h-12 w-12',
}
</script>

<template>
  <Teleport v-if="fullscreen" to="body">
    <div
      v-if="loading"
      class="fixed inset-0 z-[250] bg-background/80 backdrop-blur-sm flex items-center justify-center"
    >
      <div class="flex flex-col items-center gap-3">
        <div
          class="animate-spin rounded-full border-2 border-primary border-t-transparent"
          :class="sizeClasses[size]"
        />
        <p v-if="text" class="text-sm text-muted-foreground">{{ text }}</p>
      </div>
    </div>
  </Teleport>

  <div
    v-else-if="loading"
    class="flex flex-col items-center justify-center py-12"
  >
    <div
      class="animate-spin rounded-full border-2 border-primary border-t-transparent"
      :class="sizeClasses[size]"
    />
    <p v-if="text" class="text-sm text-muted-foreground mt-3">{{ text }}</p>
  </div>

  <slot v-else />
</template>
