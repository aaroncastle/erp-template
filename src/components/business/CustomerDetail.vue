<script setup lang="ts">
import { ref, computed } from 'vue'
import { User, Phone, Mail, MapPin, Building, Calendar, Edit, Trash2 } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import Drawer from './Drawer.vue'
import StatusBadge from './StatusBadge.vue'

export type CustomerStatus = 'active' | 'inactive' | 'prospect'

export interface CustomerDetail {
  id: string
  name: string
  contact: string
  phone: string
  email?: string
  address?: string
  company?: string
  status: CustomerStatus
  createdAt: string
  orderCount?: number
  totalAmount?: number
  remark?: string
}

interface Props {
  customer?: CustomerDetail
  open?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'edit', customer: CustomerDetail): void
  (e: 'delete', customer: CustomerDetail): void
}>()

const statusConfig = {
  active: { label: '活跃', class: 'bg-green-100 text-green-800 dark:bg-green-900/30 dark:text-green-300' },
  inactive: { label: '非活跃', class: 'bg-gray-100 text-gray-800 dark:bg-gray-900/30 dark:text-gray-300' },
  prospect: { label: '潜在客户', class: 'bg-blue-100 text-blue-800 dark:bg-blue-900/30 dark:text-blue-300' },
}

function handleEdit() {
  if (props.customer) {
    emit('edit', props.customer)
  }
}

function handleDelete() {
  if (props.customer) {
    emit('delete', props.customer)
  }
}
</script>

<template>
  <Drawer
    :open="open"
    :title="customer ? `客户详情 - ${customer.name}` : '客户详情'"
    @update:open="emit('update:open', $event)"
  >
    <!-- 左侧：客户信息 -->
    <template #list>
      <div v-if="customer" class="space-y-6">
        <!-- 基本信息 -->
        <div class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">基本信息</h3>
          <div class="space-y-2">
            <div class="flex items-center gap-2">
              <User class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">联系人：</span>
              <span class="text-sm font-medium text-foreground">{{ customer.contact }}</span>
            </div>
            <div class="flex items-center gap-2">
              <Phone class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">电话：</span>
              <span class="text-sm font-medium text-foreground">{{ customer.phone }}</span>
            </div>
            <div v-if="customer.email" class="flex items-center gap-2">
              <Mail class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">邮箱：</span>
              <span class="text-sm font-medium text-foreground">{{ customer.email }}</span>
            </div>
            <div v-if="customer.address" class="flex items-center gap-2">
              <MapPin class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">地址：</span>
              <span class="text-sm font-medium text-foreground">{{ customer.address }}</span>
            </div>
            <div v-if="customer.company" class="flex items-center gap-2">
              <Building class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">公司：</span>
              <span class="text-sm font-medium text-foreground">{{ customer.company }}</span>
            </div>
          </div>
        </div>

        <!-- 状态信息 -->
        <div class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">状态信息</h3>
          <div class="space-y-2">
            <div class="flex items-center justify-between">
              <span class="text-sm text-muted-foreground">状态：</span>
              <span
                class="px-2 py-1 text-xs rounded-md"
                :class="statusConfig[customer.status].class"
              >
                {{ statusConfig[customer.status].label }}
              </span>
            </div>
            <div class="flex items-center gap-2">
              <Calendar class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm text-muted-foreground">创建时间：</span>
              <span class="text-sm font-medium text-foreground">{{ customer.createdAt }}</span>
            </div>
          </div>
        </div>

        <!-- 业务统计 -->
        <div v-if="customer.orderCount !== undefined || customer.totalAmount !== undefined" class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">业务统计</h3>
          <div class="grid grid-cols-2 gap-4">
            <div v-if="customer.orderCount !== undefined" class="p-3 rounded-lg bg-muted/30">
              <p class="text-xs text-muted-foreground">订单数</p>
              <p class="text-2xl font-bold text-foreground mt-1">{{ customer.orderCount }}</p>
            </div>
            <div v-if="customer.totalAmount !== undefined" class="p-3 rounded-lg bg-muted/30">
              <p class="text-xs text-muted-foreground">总金额</p>
              <p class="text-2xl font-bold text-primary mt-1">¥{{ customer.totalAmount.toLocaleString() }}</p>
            </div>
          </div>
        </div>

        <!-- 备注 -->
        <div v-if="customer.remark" class="space-y-3">
          <h3 class="text-sm font-semibold text-foreground">备注</h3>
          <p class="text-sm text-muted-foreground">{{ customer.remark }}</p>
        </div>
      </div>
    </template>

    <!-- 右侧：操作区域 -->
    <template #detail>
      <div class="space-y-4">
        <h3 class="text-sm font-semibold text-foreground">操作</h3>
        <div class="space-y-2">
          <Button class="w-full" @click="handleEdit">
            <Edit class="h-4 w-4 mr-2" />
            编辑客户
          </Button>
          <Button variant="outline" class="w-full" @click="handleDelete">
            <Trash2 class="h-4 w-4 mr-2" />
            删除客户
          </Button>
        </div>
      </div>
    </template>

    <!-- 底部操作栏 -->
    <template #actions>
      <Button
        variant="outline"
        @click="emit('update:open', false)"
      >
        关闭
      </Button>
    </template>
  </Drawer>
</template>
