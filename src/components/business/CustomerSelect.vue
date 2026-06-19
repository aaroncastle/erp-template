<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, User, Clock, Check } from '@lucide/vue'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Card, CardContent } from '@/components/ui/card'

export interface Customer {
  id: string
  name: string
  contact?: string
  phone?: string
  address?: string
  recent?: boolean // 最近使用
}

interface Props {
  open?: boolean
  customers?: Customer[]
  recentCustomers?: Customer[]
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
  customers: () => [],
  recentCustomers: () => [],
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'select', customerId: string, customerName: string): void
}>()

const searchQuery = ref('')
const selectedId = ref<string>('')

// 过滤客户列表
const filteredCustomers = computed(() => {
  if (!searchQuery.value.trim()) return props.customers
  const query = searchQuery.value.toLowerCase()
  return props.customers.filter(
    (customer) =>
      customer.name.toLowerCase().includes(query) ||
      customer.contact?.toLowerCase().includes(query) ||
      customer.phone?.includes(query)
  )
})

// 选择客户
function selectCustomer(customer: Customer) {
  selectedId.value = customer.id
  emit('select', customer.id, customer.name)
  close()
}

// 关闭
function close() {
  emit('update:open', false)
  searchQuery.value = ''
  selectedId.value = ''
}
</script>

<template>
  <Teleport to="body">
    <div
      v-if="open"
      class="fixed inset-0 z-[200] bg-black/50 backdrop-blur-sm flex items-center justify-center"
      @click.self="close"
    >
      <Card class="w-[600px] max-h-[80vh] flex flex-col">
        <div class="p-4 border-b border-border">
          <div class="flex items-center justify-between mb-3">
            <h2 class="text-lg font-semibold text-foreground">选择客户</h2>
            <Button variant="ghost" size="sm" @click="close">
              关闭
            </Button>
          </div>

          <!-- 搜索框 -->
          <div class="relative">
            <Search class="absolute left-3 top-1/2 -translate-y-1/2 h-4 w-4 text-muted-foreground" />
            <Input
              v-model="searchQuery"
              placeholder="搜索客户名称、联系人或电话..."
              class="pl-10"
            />
          </div>
        </div>

        <CardContent class="flex-1 overflow-y-auto p-4">
          <!-- 最近使用 -->
          <div v-if="recentCustomers.length > 0 && !searchQuery" class="mb-4">
            <div class="flex items-center gap-2 mb-2">
              <Clock class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm font-medium text-foreground">最近使用</span>
            </div>
            <div class="space-y-1">
              <div
                v-for="customer in recentCustomers"
                :key="customer.id"
                class="flex items-center justify-between p-2 rounded-md hover:bg-accent cursor-pointer transition-colors"
                @click="selectCustomer(customer)"
              >
                <div class="flex items-center gap-3">
                  <User class="h-5 w-5 text-muted-foreground" />
                  <div>
                    <div class="text-sm font-medium text-foreground">{{ customer.name }}</div>
                    <div v-if="customer.contact" class="text-xs text-muted-foreground">
                      {{ customer.contact }}
                    </div>
                  </div>
                </div>
                <Check
                  v-if="selectedId === customer.id"
                  class="h-4 w-4 text-primary"
                />
              </div>
            </div>
          </div>

          <!-- 全部客户 -->
          <div>
            <div v-if="filteredCustomers.length > 0" class="space-y-1">
              <div
                v-for="customer in filteredCustomers"
                :key="customer.id"
                class="flex items-center justify-between p-3 rounded-md hover:bg-accent cursor-pointer transition-colors border border-border"
                @click="selectCustomer(customer)"
              >
                <div class="flex items-center gap-3 flex-1">
                  <User class="h-5 w-5 text-muted-foreground" />
                  <div class="flex-1">
                    <div class="flex items-center gap-2">
                      <span class="text-sm font-medium text-foreground">{{ customer.name }}</span>
                      <span v-if="customer.recent" class="text-xs text-muted-foreground bg-muted px-2 py-0.5 rounded">
                        最近
                      </span>
                    </div>
                    <div v-if="customer.contact || customer.phone" class="text-xs text-muted-foreground mt-1">
                      <span v-if="customer.contact">{{ customer.contact }}</span>
                      <span v-if="customer.contact && customer.phone"> · </span>
                      <span v-if="customer.phone">{{ customer.phone }}</span>
                    </div>
                    <div v-if="customer.address" class="text-xs text-muted-foreground mt-1">
                      {{ customer.address }}
                    </div>
                  </div>
                </div>
                <Check
                  v-if="selectedId === customer.id"
                  class="h-4 w-4 text-primary"
                />
              </div>
            </div>

            <!-- 空状态 -->
            <div v-else class="text-center py-8">
              <User class="h-12 w-12 mx-auto text-muted-foreground mb-2" />
              <p class="text-muted-foreground">
                {{ searchQuery ? '未找到匹配的客户' : '暂无客户数据' }}
              </p>
            </div>
          </div>
        </CardContent>
      </Card>
    </div>
  </Teleport>
</template>
