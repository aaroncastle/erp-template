<script setup lang="ts">
import { ref, computed } from 'vue'
import { Plus, Edit, Trash2, Search } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Card, CardContent } from '@/components/ui/card'
import StatusBadge from './StatusBadge.vue'
import EmptyState from './EmptyState.vue'

export type ProductStatus = 'active' | 'inactive' | 'discontinued'

export interface Product {
  id: string
  name: string
  specification?: string
  category?: string
  unit?: string
  price: number
  cost?: number
  stock?: number
  status: ProductStatus
  description?: string
}

interface Props {
  products?: Product[]
  title?: string
}

const props = withDefaults(defineProps<Props>(), {
  products: () => [],
  title: '产品列表',
})

const emit = defineEmits<{
  (e: 'add'): void
  (e: 'edit', product: Product): void
  (e: 'delete', product: Product): void
  (e: 'select', product: Product): void
}>()

const searchQuery = ref('')

const filteredProducts = computed(() => {
  if (!searchQuery.value.trim()) return props.products

  const query = searchQuery.value.toLowerCase()
  return props.products.filter(product =>
    product.name.toLowerCase().includes(query) ||
    product.specification?.toLowerCase().includes(query) ||
    product.category?.toLowerCase().includes(query)
  )
})

const statusConfig = {
  active: { label: '在售', class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300' },
  inactive: { label: '下架', class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300' },
  discontinued: { label: '停产', class: 'bg-red-100 text-red-800 dark:bg-red-900/30 dark:text-red-300' },
}
</script>

<template>
  <Card>
    <CardContent class="p-4">
      <!-- 头部 -->
      <div class="flex items-center justify-between mb-4">
        <h3 class="text-lg font-semibold text-foreground">{{ title }}</h3>
        <Button @click="emit('add')">
          <Plus class="h-4 w-4 mr-2" />
          添加产品
        </Button>
      </div>

      <!-- 搜索 -->
      <div class="relative mb-4">
        <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
        <Input
          v-model="searchQuery"
          placeholder="搜索产品名称、规格或分类..."
          class="pl-10"
        />
      </div>

      <!-- 产品列表 -->
      <div v-if="filteredProducts.length > 0" class="space-y-2">
        <div
          v-for="product in filteredProducts"
          :key="product.id"
          class="flex items-center justify-between p-3 border border-border rounded-lg hover:bg-muted/30 cursor-pointer transition-colors"
          @click="emit('select', product)"
        >
          <div class="flex-1">
            <div class="flex items-center gap-2">
              <h4 class="text-sm font-medium text-foreground">{{ product.name }}</h4>
              <span
                class="px-2 py-0.5 text-xs rounded-md"
                :class="statusConfig[product.status].class"
              >
                {{ statusConfig[product.status].label }}
              </span>
            </div>
            <div class="flex items-center gap-4 mt-1 text-xs text-muted-foreground">
              <span v-if="product.specification">规格：{{ product.specification }}</span>
              <span v-if="product.category">分类：{{ product.category }}</span>
              <span v-if="product.stock !== undefined">库存：{{ product.stock }}</span>
            </div>
          </div>
          <div class="flex items-center gap-4">
            <div class="text-right">
              <p class="text-sm font-medium text-primary">¥{{ product.price.toFixed(2) }}</p>
              <p v-if="product.cost" class="text-xs text-muted-foreground">成本：¥{{ product.cost.toFixed(2) }}</p>
            </div>
            <div class="flex items-center gap-1">
              <Button variant="ghost" size="sm" @click.stop="emit('edit', product)">
                <Edit class="h-4 w-4" />
              </Button>
              <Button variant="ghost" size="sm" @click.stop="emit('delete', product)">
                <Trash2 class="h-4 w-4 text-destructive" />
              </Button>
            </div>
          </div>
        </div>
      </div>

      <!-- 空状态 -->
      <EmptyState
        v-else
        type="search"
        title="未找到产品"
        description="尝试使用其他搜索条件"
      />
    </CardContent>
  </Card>
</template>
