<script setup lang="ts">
import { ref, computed } from 'vue'
import { Plus, Trash2, Save, X } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import CustomerSelect from './CustomerSelect.vue'

export interface OrderItem {
  id: string
  productName: string
  specification: string
  quantity: number
  unitPrice: number
  amount: number
  // 阴阳项目关联
  customerProductName?: string
  customerSpecification?: string
  customerQuantity?: number
  customerUnitPrice?: number
  customerAmount?: number
}

export interface OrderData {
  id?: string
  orderNumber?: string
  customerId: string
  customerName: string
  items: OrderItem[]
  totalAmount: number
  remark?: string
}

interface Props {
  order?: OrderData
  mode?: 'create' | 'edit'
}

const props = withDefaults(defineProps<Props>(), {
  mode: 'create',
})

const emit = defineEmits<{
  (e: 'save', data: OrderData): void
  (e: 'cancel'): void
}>()

// 表单数据
const form = ref<OrderData>({
  id: props.order?.id || '',
  orderNumber: props.order?.orderNumber || '',
  customerId: props.order?.customerId || '',
  customerName: props.order?.customerName || '',
  items: props.order?.items || [],
  totalAmount: props.order?.totalAmount || 0,
  remark: props.order?.remark || '',
})

// 当前编辑的订单项
const editingItem = ref<OrderItem>({
  id: '',
  productName: '',
  specification: '',
  quantity: 1,
  unitPrice: 0,
  amount: 0,
})

// 是否显示客户选择器
const showCustomerSelect = ref(false)

// 计算总金额
function calculateTotal() {
  form.value.totalAmount = form.value.items.reduce((sum, item) => sum + item.amount, 0)
}

// 添加订单项
function addItem() {
  if (!editingItem.value.productName.trim()) return

  const newItem: OrderItem = {
    ...editingItem.value,
    id: Date.now().toString(),
    amount: editingItem.value.quantity * editingItem.value.unitPrice,
    customerAmount: editingItem.value.customerUnitPrice
      ? editingItem.value.customerQuantity! * editingItem.value.customerUnitPrice
      : 0,
  }

  form.value.items.push(newItem)
  calculateTotal()

  // 重置编辑项
  editingItem.value = {
    id: '',
    productName: '',
    specification: '',
    quantity: 1,
    unitPrice: 0,
    amount: 0,
  }
}

// 删除订单项
function removeItem(index: number) {
  form.value.items.splice(index, 1)
  calculateTotal()
}

// 选择客户
function selectCustomer(customerId: string, customerName: string) {
  form.value.customerId = customerId
  form.value.customerName = customerName
  showCustomerSelect.value = false
}

// 保存订单
function handleSave() {
  if (!form.value.customerName.trim()) {
    alert('请选择客户')
    return
  }
  if (form.value.items.length === 0) {
    alert('请至少添加一个产品')
    return
  }
  emit('save', form.value)
}

// 取消
function handleCancel() {
  emit('cancel')
}
</script>

<template>
  <Card class="w-full">
    <CardHeader>
      <CardTitle>{{ mode === 'create' ? '创建订单' : '编辑订单' }}</CardTitle>
    </CardHeader>
    <CardContent class="space-y-6">
      <!-- 客户选择 -->
      <div class="space-y-2">
        <Label>客户</Label>
        <div class="flex items-center gap-2">
          <div class="flex-1 px-3 py-2 border border-input rounded-md bg-muted">
            {{ form.customerName || '请选择客户' }}
          </div>
          <Button variant="outline" @click="showCustomerSelect = true">
            选择
          </Button>
        </div>
      </div>

      <!-- 订单项列表 -->
      <div class="space-y-3">
        <Label>订单项</Label>

        <!-- 已添加的订单项 -->
        <div v-if="form.items.length > 0" class="space-y-2">
          <div
            v-for="(item, index) in form.items"
            :key="item.id"
            class="p-3 border border-border rounded-md bg-muted/30"
          >
            <div class="flex items-start justify-between">
              <div class="flex-1 space-y-1">
                <div class="flex items-center gap-2">
                  <span class="font-medium text-foreground">{{ item.productName }}</span>
                  <span class="text-xs text-muted-foreground">{{ item.specification }}</span>
                </div>
                <div class="flex items-center gap-4 text-sm">
                  <span class="text-muted-foreground">数量：{{ item.quantity }}</span>
                  <span class="text-muted-foreground">单价：¥{{ item.unitPrice.toFixed(2) }}</span>
                  <span class="font-medium text-primary">金额：¥{{ item.amount.toFixed(2) }}</span>
                </div>
                <!-- 阴阳项目信息 -->
                <div v-if="item.customerProductName" class="mt-2 pt-2 border-t border-border text-xs text-muted-foreground">
                  <div>客户指定：{{ item.customerProductName }} {{ item.customerSpecification }}</div>
                  <div>客户金额：¥{{ item.customerAmount?.toFixed(2) }}</div>
                </div>
              </div>
              <Button variant="ghost" size="sm" @click="removeItem(index)">
                <Trash2 class="h-4 w-4 text-destructive" />
              </Button>
            </div>
          </div>
        </div>

        <!-- 添加订单项表单 -->
        <div class="p-4 border border-border rounded-md bg-muted/20 space-y-3">
          <div class="grid grid-cols-2 gap-3">
            <div class="space-y-1">
              <Label class="text-xs">产品名称</Label>
              <Input v-model="editingItem.productName" placeholder="输入产品名称" />
            </div>
            <div class="space-y-1">
              <Label class="text-xs">规格</Label>
              <Input v-model="editingItem.specification" placeholder="输入规格" />
            </div>
          </div>
          <div class="grid grid-cols-2 gap-3">
            <div class="space-y-1">
              <Label class="text-xs">数量</Label>
              <Input v-model.number="editingItem.quantity" type="number" min="1" />
            </div>
            <div class="space-y-1">
              <Label class="text-xs">单价</Label>
              <Input v-model.number="editingItem.unitPrice" type="number" min="0" step="0.01" />
            </div>
          </div>

          <!-- 阴阳项目（可选） -->
          <div class="pt-3 border-t border-border">
            <div class="flex items-center gap-2 mb-2">
              <Label class="text-xs">客户指定项（可选）</Label>
            </div>
            <div class="grid grid-cols-2 gap-3">
              <div class="space-y-1">
                <Label class="text-xs">客户产品名称</Label>
                <Input v-model="editingItem.customerProductName" placeholder="输入客户指定产品名" />
              </div>
              <div class="space-y-1">
                <Label class="text-xs">客户规格</Label>
                <Input v-model="editingItem.customerSpecification" placeholder="输入客户指定规格" />
              </div>
            </div>
            <div class="grid grid-cols-2 gap-3 mt-2">
              <div class="space-y-1">
                <Label class="text-xs">客户数量</Label>
                <Input v-model.number="editingItem.customerQuantity" type="number" min="1" />
              </div>
              <div class="space-y-1">
                <Label class="text-xs">客户单价</Label>
                <Input v-model.number="editingItem.customerUnitPrice" type="number" min="0" step="0.01" />
              </div>
            </div>
          </div>

          <Button @click="addItem" class="w-full" :disabled="!editingItem.productName.trim()">
            <Plus class="h-4 w-4 mr-1" />
            添加产品
          </Button>
        </div>
      </div>

      <!-- 备注 -->
      <div class="space-y-2">
        <Label>备注</Label>
        <Input v-model="form.remark" placeholder="输入备注信息（可选）" />
      </div>

      <!-- 总金额 -->
      <div class="flex items-center justify-between p-4 border border-border rounded-md bg-primary/5">
        <span class="text-sm font-medium text-foreground">总金额</span>
        <span class="text-2xl font-bold text-primary">¥{{ form.totalAmount.toFixed(2) }}</span>
      </div>

      <!-- 操作按钮 -->
      <div class="flex items-center justify-end gap-2 pt-4 border-t border-border">
        <Button variant="outline" @click="handleCancel">
          <X class="h-4 w-4 mr-1" />
          取消
        </Button>
        <Button @click="handleSave">
          <Save class="h-4 w-4 mr-1" />
          保存
        </Button>
      </div>
    </CardContent>

    <!-- 客户选择器 -->
    <CustomerSelect
      v-model:open="showCustomerSelect"
      @select="selectCustomer"
    />
  </Card>
</template>
