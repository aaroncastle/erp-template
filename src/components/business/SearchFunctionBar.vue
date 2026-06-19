<script setup lang="ts">
import { ref } from 'vue'
import { Search, Plus } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'

interface Props {
  search?: string
  placeholder?: string
  showCreate?: boolean
  createText?: string
}

const props = withDefaults(defineProps<Props>(), {
  search: '',
  placeholder: '搜索...',
  showCreate: true,
  createText: '新建',
})

const emit = defineEmits<{
  (e: 'update:search', value: string): void
  (e: 'create'): void
}>()

function handleSearch(event: Event) {
  const target = event.target as HTMLInputElement
  emit('update:search', target.value)
}

function handleCreate() {
  emit('create')
}
</script>

<template>
  <div class="space-y-3">
    <!-- 搜索框 -->
    <div class="relative">
      <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
      <Input
        :value="search"
        :placeholder="placeholder"
        class="pl-10"
        @input="handleSearch"
      />
    </div>

    <!-- 功能按钮 -->
    <div v-if="showCreate" class="flex gap-2">
      <Button class="flex-1" @click="handleCreate">
        <Plus class="h-4 w-4 mr-1" />
        {{ createText }}
      </Button>
    </div>
  </div>
</template>
