<script setup lang="ts">
import { ref, computed } from 'vue'
import { FileText, Check, X, AlertCircle, Upload, Copy, Eye, Link } from '@lucide/vue'
import StatusBadge from './StatusBadge.vue'

export type InvoiceMode = 'sales' | 'finance'

interface InvoiceItem {
  id: string
  orderNumber: string
  customerName: string
  productName: string
  specification: string
  quantity: number
  unitPrice: number
  amount: number
  customContent?: string // 销售自定义的开票内容
}

interface Props {
  items: InvoiceItem[]
  mode?: InvoiceMode
  open?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  mode: 'sales',
  open: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'submit', items: InvoiceItem[]): void
  (e: 'upload', file: File, items: InvoiceItem[]): void
  (e: 'cancel'): void
}>()

const selectedItems = ref<Set<string>>(new Set())
const customContent = ref('')
const step = ref(1)
const uploadedFile = ref<File | null>(null)
const previewUrl = ref('')
const copiedContent = ref(false)

// 销售模式：3 步流程
// 财务模式：4 步流程
const totalSteps = computed(() => props.mode === 'sales' ? 3 : 4)

const isAllSelected = computed(() => {
  return props.items.length > 0 && selectedItems.value.size === props.items.length
})

const selectedCount = computed(() => selectedItems.value.size)

const totalAmount = computed(() => {
  return displayItems.value
    .reduce((sum, item) => sum + item.amount, 0)
})

const displayItems = computed(() => {
  // 财务模式：所有项目都是选中状态（销售已提交）
  if (props.mode === 'finance') {
    return props.items
  }
  // 销售模式：只显示选中的项目
  return props.items.filter(item => selectedItems.value.has(item.id))
})

function toggleSelectAll() {
  if (isAllSelected.value) {
    selectedItems.value.clear()
  } else {
    props.items.forEach(item => {
      selectedItems.value.add(item.id)
    })
  }
}

function toggleSelect(item: InvoiceItem) {
  if (selectedItems.value.has(item.id)) {
    selectedItems.value.delete(item.id)
  } else {
    selectedItems.value.add(item.id)
  }
}

function isSelected(id: string): boolean {
  return selectedItems.value.has(id)
}

function nextStep() {
  if (step.value < totalSteps.value) {
    step.value++
  }
}

function prevStep() {
  if (step.value > 1) {
    step.value--
  }
}

function handleConfirm() {
  const selected = props.items.filter(item => selectedItems.value.has(item.id))
  emit('submit', selected)
  close()
}

function handleUpload() {
  if (uploadedFile.value) {
    emit('upload', uploadedFile.value, displayItems.value)
  }
  close()
}

function copyContent() {
  const content = displayItems.value
    .map(item => `${item.productName} - ${item.specification} - ¥${item.amount.toFixed(2)}`)
    .join('\n')

  navigator.clipboard.writeText(content).then(() => {
    copiedContent.value = true
    setTimeout(() => {
      copiedContent.value = false
    }, 2000)
  })
}

function handleFileChange(event: Event) {
  const input = event.target as HTMLInputElement
  if (input.files && input.files.length > 0) {
    uploadedFile.value = input.files[0]
    // 模拟生成预览 URL
    previewUrl.value = URL.createObjectURL(input.files[0])
  }
}

function close() {
  emit('update:open', false)
  // 重置状态
  setTimeout(() => {
    selectedItems.value.clear()
    customContent.value = ''
    step.value = 1
    uploadedFile.value = null
    previewUrl.value = ''
  }, 300)
}

// 步骤标题
const stepTitles = {
  sales: ['选择开票项目', '填写自定义内容', '确认申请'],
  finance: ['查看申请项目', '预览开票内容', '上传发票', '完成'],
}

const stepTitle = computed(() => {
  const titles = stepTitles[props.mode]
  return `步骤 ${step.value}/${totalSteps.value} - ${titles[step.value - 1]}`
})
</script>

<template>
  <div
    v-if="open"
    class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm"
    @click.self="close"
  >
    <div class="w-[800px] max-h-[90vh] bg-background border border-border rounded-lg shadow-xl flex flex-col">
      <!-- 头部 -->
      <div class="flex items-center justify-between p-4 border-b border-border">
        <div class="flex items-center gap-3">
          <div class="inline-flex items-center justify-center h-10 w-10 rounded-lg bg-primary/10 text-primary">
            <FileText class="h-5 w-5" />
          </div>
          <div>
            <h2 class="text-lg font-semibold text-foreground">
              {{ mode === 'sales' ? '申请开票' : '上传发票' }}
            </h2>
            <p class="text-sm text-muted-foreground">{{ stepTitle }}</p>
          </div>
        </div>
        <button
          class="inline-flex items-center justify-center rounded-md h-8 w-8 text-muted-foreground hover:text-foreground hover:bg-accent transition-colors"
          @click="close"
        >
          <X class="h-5 w-5" />
        </button>
      </div>

      <!-- 步骤指示器 -->
      <div class="flex items-center justify-center p-4 border-b border-border">
        <div class="flex items-center gap-2">
          <div v-for="i in totalSteps" :key="i" class="flex items-center gap-2">
            <div
              class="inline-flex items-center justify-center h-8 w-8 rounded-full text-sm font-medium transition-colors"
              :class="step >= i ? 'bg-primary text-primary-foreground' : 'bg-muted text-muted-foreground'"
            >
              <Check v-if="step > i" class="h-4 w-4" />
              <span v-else>{{ i }}</span>
            </div>
            <span v-if="i < totalSteps" class="text-sm text-muted-foreground" :class="step > i ? 'text-primary' : ''">
              →
            </span>
          </div>
        </div>
      </div>

      <!-- 内容区域 -->
      <div class="flex-1 overflow-y-auto p-4">
        <!-- 步骤 1: 选择/查看项目 -->
        <div v-if="step === 1" class="space-y-3">
          <!-- 销售模式：可选择 -->
          <div v-if="mode === 'sales'" class="flex items-center justify-between p-3 rounded-lg border border-border bg-muted/20">
            <div class="flex items-center gap-3">
              <button
                class="inline-flex items-center justify-center rounded-md h-8 w-8 hover:bg-accent transition-colors"
                :class="isAllSelected ? 'text-primary' : 'text-muted-foreground'"
                @click="toggleSelectAll"
              >
                <Check v-if="isAllSelected" class="h-5 w-5" />
                <X v-else class="h-5 w-5" />
              </button>
              <span class="text-sm text-muted-foreground">
                已选择 <span class="font-medium text-foreground">{{ selectedCount }}</span> 项
              </span>
            </div>
            <span class="text-sm text-muted-foreground">
              合计金额：<span class="font-semibold text-primary">¥{{ totalAmount.toFixed(2) }}</span>
            </span>
          </div>

          <!-- 财务模式：提示这是销售提交的申请 -->
          <div v-else class="p-3 rounded-lg border border-border bg-blue-50/50 dark:bg-blue-900/10">
            <div class="flex items-start gap-2">
              <AlertCircle class="h-5 w-5 text-blue-500 flex-shrink-0 mt-0.5" />
              <div>
                <p class="text-sm font-medium text-blue-900 dark:text-blue-100">销售提交的开票申请</p>
                <p class="text-xs text-blue-700 dark:text-blue-300 mt-0.5">
                  以下为销售选择的项目，已自动选中，不可取消
                </p>
              </div>
            </div>
          </div>

          <div class="space-y-2">
            <div
              v-for="item in items"
              :key="item.id"
              class="rounded-lg border border-border p-3"
              :class="{
                'cursor-pointer hover:bg-accent transition-colors': mode === 'sales',
                'bg-muted/20': mode === 'finance' || isSelected(item.id),
                'border-primary': mode === 'finance'
              }"
              @click="mode === 'sales' && toggleSelect(item)"
            >
              <div class="flex items-start gap-3">
                <!-- 选择框（销售模式） -->
                <div v-if="mode === 'sales'" class="flex-shrink-0">
                  <div
                    class="inline-flex items-center justify-center rounded-md h-5 w-5 border border-border mt-0.5"
                    :class="isSelected(item.id) ? 'bg-primary border-primary' : ''"
                  >
                    <Check v-if="isSelected(item.id)" class="h-3 w-3 text-primary-foreground" />
                  </div>
                </div>

                <!-- 已选标记（财务模式） -->
                <div v-else class="flex-shrink-0">
                  <Check class="h-5 w-5 text-primary" />
                </div>

                <div class="flex-1">
                  <div class="flex items-center justify-between">
                    <div class="flex items-center gap-2">
                      <span class="text-sm font-medium text-foreground">{{ item.productName }}</span>
                      <span class="text-xs text-muted-foreground">{{ item.specification }}</span>
                    </div>
                    <span class="text-sm font-semibold text-foreground">¥{{ item.amount.toFixed(2) }}</span>
                  </div>
                  <div class="flex items-center gap-4 mt-1 text-xs text-muted-foreground">
                    <span>订单：{{ item.orderNumber }}</span>
                    <span>客户：{{ item.customerName }}</span>
                    <span>数量：{{ item.quantity }}</span>
                    <span>单价：¥{{ item.unitPrice.toFixed(2) }}</span>
                  </div>
                  <!-- 显示销售自定义内容 -->
                  <div v-if="item.customContent" class="mt-2 p-2 rounded-md bg-muted/30 text-xs">
                    <div class="flex items-center gap-1 text-muted-foreground mb-1">
                      <FileText class="h-3 w-3" />
                      <span>自定义开票内容：</span>
                    </div>
                    <p class="text-foreground whitespace-pre-wrap">{{ item.customContent }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div v-if="items.length === 0" class="text-center py-8">
            <AlertCircle class="h-12 w-12 mx-auto text-muted-foreground mb-2" />
            <p class="text-muted-foreground">暂无开票项目</p>
          </div>
        </div>

        <!-- 步骤 2: 自定义内容（销售）/ 预览内容（财务） -->
        <div v-else-if="step === 2" class="space-y-4">
          <!-- 销售模式：可编辑自定义内容 -->
          <div v-if="mode === 'sales'" class="space-y-2">
            <label class="text-sm font-medium text-foreground">自定义开票内容</label>
            <textarea
              v-model="customContent"
              rows="6"
              placeholder="不填写即以实际订单项内容为开票内容"
              class="w-full rounded-md border border-input bg-background px-3 py-2 text-sm placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring"
            />
            <p class="text-xs text-muted-foreground">
              提示：如需特殊开票内容请在此填写，留空则按订单明细开票
            </p>
          </div>

          <!-- 财务模式：预览销售填写的内容（不可编辑，可复制） -->
          <div v-else class="space-y-2">
            <div class="flex items-center justify-between">
              <label class="text-sm font-medium text-foreground">开票内容预览</label>
              <button
                class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
                @click="copyContent"
              >
                <Check v-if="copiedContent" class="h-3.5 w-3.5 text-green-500" />
                <Copy v-else class="h-3.5 w-3.5" />
                {{ copiedContent ? '已复制' : '复制内容' }}
              </button>
            </div>

            <div class="rounded-lg border border-border p-4 bg-muted/20">
              <div class="space-y-3">
                <div v-for="item in displayItems" :key="item.id" class="pb-3 border-b border-border last:border-b-0 last:pb-0">
                  <div class="flex items-center justify-between mb-1">
                    <span class="text-sm font-medium text-foreground">{{ item.productName }}</span>
                    <span class="text-sm font-semibold text-foreground">¥{{ item.amount.toFixed(2) }}</span>
                  </div>
                  <div class="text-xs text-muted-foreground space-y-1">
                    <p>{{ item.specification }}</p>
                    <p>{{ item.orderNumber }} · {{ item.customerName }}</p>
                    <p>数量：{{ item.quantity }} · 单价：¥{{ item.unitPrice.toFixed(2) }}</p>
                  </div>
                  <!-- 销售自定义内容 -->
                  <div v-if="item.customContent" class="mt-2 p-2 rounded-md bg-background border border-border">
                    <p class="text-xs font-medium text-muted-foreground mb-1">自定义开票内容：</p>
                    <p class="text-sm text-foreground whitespace-pre-wrap">{{ item.customContent }}</p>
                  </div>
                </div>
              </div>

              <div class="flex items-center justify-between pt-3 mt-3 border-t border-border">
                <span class="text-sm font-medium text-foreground">合计金额</span>
                <span class="text-lg font-semibold text-primary">¥{{ totalAmount.toFixed(2) }}</span>
              </div>
            </div>

            <p v-if="!displayItems[0]?.customContent" class="text-xs text-muted-foreground">
              销售未填写自定义内容，将以实际订单项内容为开票内容
            </p>
          </div>
        </div>

        <!-- 步骤 3: 确认（销售）/ 上传发票（财务） -->
        <div v-else-if="step === 3" class="space-y-4">
          <!-- 销售模式：确认申请 -->
          <div v-if="mode === 'sales'" class="space-y-4">
            <div class="rounded-lg border border-border p-4">
              <div class="grid grid-cols-2 gap-4 text-sm">
                <div class="flex items-center justify-between">
                  <span class="text-muted-foreground">开票项目：</span>
                  <span class="font-medium text-foreground">{{ selectedCount }} 项</span>
                </div>
                <div class="flex items-center justify-between">
                  <span class="text-muted-foreground">合计金额：</span>
                  <span class="font-semibold text-primary">¥{{ totalAmount.toFixed(2) }}</span>
                </div>
              </div>
            </div>

            <div v-if="customContent" class="rounded-lg border border-border p-4">
              <h3 class="text-sm font-semibold text-foreground mb-2">自定义开票内容</h3>
              <p class="text-sm text-muted-foreground whitespace-pre-wrap">{{ customContent }}</p>
            </div>

            <div class="rounded-lg border border-border p-4">
              <h3 class="text-sm font-semibold text-foreground mb-3">开票项目明细</h3>
              <div class="space-y-2">
                <div
                  v-for="item in displayItems"
                  :key="item.id"
                  class="flex items-center justify-between py-2 border-b border-border last:border-b-0"
                >
                  <div>
                    <div class="text-sm font-medium text-foreground">{{ item.productName }}</div>
                    <div class="text-xs text-muted-foreground">
                      {{ item.orderNumber }} · {{ item.customerName }}
                    </div>
                  </div>
                  <span class="text-sm font-semibold text-foreground">¥{{ item.amount.toFixed(2) }}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- 财务模式：上传发票 -->
          <div v-else class="space-y-4">
            <div class="rounded-lg border-2 border-dashed border-border p-8 text-center">
              <Upload class="h-12 w-12 mx-auto text-muted-foreground mb-3" />
              <p class="text-sm font-medium text-foreground mb-1">点击或拖拽上传发票</p>
              <p class="text-xs text-muted-foreground mb-4">支持 PDF、JPG、PNG 格式</p>

              <input
                type="file"
                accept=".pdf,.jpg,.jpeg,.png"
                class="hidden"
                @change="handleFileChange"
              />
              <button
                class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
                @click="$el.querySelector('input[type=file]')?.click()"
              >
                选择文件
              </button>

              <!-- 已上传文件预览 -->
              <div v-if="uploadedFile" class="mt-4 p-3 rounded-lg bg-muted/20 text-left">
                <div class="flex items-center justify-between">
                  <div class="flex items-center gap-2">
                    <FileText class="h-5 w-5 text-primary" />
                    <div>
                      <p class="text-sm font-medium text-foreground">{{ uploadedFile.name }}</p>
                      <p class="text-xs text-muted-foreground">
                        {{ (uploadedFile.size / 1024).toFixed(2) }} KB
                      </p>
                    </div>
                  </div>
                  <button
                    class="text-muted-foreground hover:text-foreground transition-colors"
                    @click="uploadedFile = null; previewUrl = ''"
                  >
                    <X class="h-4 w-4" />
                  </button>
                </div>

                <div v-if="previewUrl" class="mt-3 flex items-center gap-2">
                  <Link class="h-4 w-4 text-muted-foreground" />
                  <a
                    :href="previewUrl"
                    target="_blank"
                    class="text-sm text-primary hover:underline"
                  >
                    预览发票
                  </a>
                  <button
                    class="text-muted-foreground hover:text-foreground transition-colors"
                    @click="navigator.clipboard.writeText(previewUrl)"
                  >
                    <Copy class="h-3.5 w-3.5" />
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 步骤 4: 完成（仅财务模式） -->
        <div v-else-if="step === 4" class="space-y-4">
          <div class="text-center py-8">
            <Check class="h-16 w-16 mx-auto text-green-500 mb-3" />
            <h3 class="text-lg font-semibold text-foreground mb-2">发票上传成功</h3>
            <p class="text-sm text-muted-foreground">
              发票已上传并通知销售人员
            </p>
          </div>

          <div class="rounded-lg border border-border p-4">
            <div class="space-y-3">
              <div class="flex items-center justify-between text-sm">
                <span class="text-muted-foreground">发票文件：</span>
                <span class="font-medium text-foreground">{{ uploadedFile?.name }}</span>
              </div>
              <div class="flex items-center justify-between text-sm">
                <span class="text-muted-foreground">开票项目：</span>
                <span class="font-medium text-foreground">{{ displayItems.length }} 项</span>
              </div>
              <div class="flex items-center justify-between text-sm">
                <span class="text-muted-foreground">合计金额：</span>
                <span class="font-semibold text-primary">¥{{ totalAmount.toFixed(2) }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 底部操作栏 -->
      <div class="flex items-center justify-between p-4 border-t border-border">
        <div class="text-sm text-muted-foreground">
          <span v-if="mode === 'sales'">
            <span v-if="step === 1">请选择需要开票的项目</span>
            <span v-else-if="step === 2">请填写自定义开票内容（可选）</span>
            <span v-else>请确认开票信息</span>
          </span>
          <span v-else>
            <span v-if="step === 1">请预览申请开票的项目</span>
            <span v-else-if="step === 2">请预览开票内容</span>
            <span v-else-if="step === 3">请上传实际发票</span>
            <span v-else>开票流程已完成</span>
          </span>
        </div>
        <div class="flex items-center gap-2">
          <button
            v-if="step > 1 && step < totalSteps"
            class="px-4 py-2 text-sm font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
            @click="prevStep"
          >
            上一步
          </button>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
            @click="close"
          >
            取消
          </button>
          <button
            v-if="step < totalSteps"
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            :disabled="mode === 'sales' && step === 1 && selectedCount === 0"
            @click="nextStep"
          >
            下一步
          </button>
          <button
            v-else-if="mode === 'sales'"
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="handleConfirm"
          >
            申请开票
          </button>
          <button
            v-else-if="step === 3"
            class="inline-flex items-center gap-1.5 px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            :disabled="!uploadedFile"
            @click="handleUpload"
          >
            <Upload class="h-4 w-4" />
            上传发票
          </button>
          <button
            v-else
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="close"
          >
            完成
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
