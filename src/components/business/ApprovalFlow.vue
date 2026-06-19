<script setup lang="ts">
import { ref, computed } from 'vue'
import { CheckCircle2, XCircle, Clock, MessageSquare, Send, Info, Plus, X, User } from '@lucide/vue'
import StatusBadge from './StatusBadge.vue'

export type ApprovalStatus = 'pending' | 'approved' | 'rejected' | 'cancelled'

export interface ApprovalRecord {
  id: string
  approver: string
  department: string
  status: ApprovalStatus
  comment?: string
  timestamp: string
}

export interface ModificationItem {
  field: string
  before: string
  after: string
}

export interface BusinessData {
  type: 'order' | 'purchase' | 'customer_info'
  // 订单类型（综合办只能看到客户名称）
  customerName?: string
  totalAmount?: number
  orderNumber?: string
  // 修改内容（订单修改申请）
  modifications?: ModificationItem[]
  // 采购类型
  items?: {
    productName: string
    quantity: number
    unit: string
    amount: number
  }[]
}

export interface CcPerson {
  name: string
  department?: string
}

export interface ApprovalFlow {
  id: string
  title: string
  status: ApprovalStatus
  records: ApprovalRecord[]
  currentStep: number
  totalSteps: number
  submitter?: string // 提交人
  submitComment?: string // 提交原因
  businessData?: BusinessData // 业务数据
  ccList?: CcPerson[] // 抄送人列表
}

interface Props {
  flow?: ApprovalFlow
  open?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'approve', comment: string, ccList: CcPerson[]): void
  (e: 'reject', comment: string, ccList: CcPerson[]): void
  (e: 'close'): void
}>()

const comment = ref('')
const showCommentInput = ref(false)
const actionType = ref<'approve' | 'reject'>('approve')
const ccList = ref<CcPerson[]>([])
const showCcInput = ref(false)
const newCcName = ref('')
const newCcDepartment = ref('')

// 驳回时原因必填
// 计算已通过的步骤数（用于进度条显示）
const approvedSteps = computed(() => {
  if (!props.flow) return 0
  return props.flow.records.filter(record => record.status === 'approved').length
})

// 当前步骤 = 已通过步骤数 + 1（当前正在审批的步骤）
const currentStep = computed(() => {
  if (!props.flow) return 1
  return approvedSteps.value + 1
})

const isRejectCommentRequired = computed(() => actionType.value === 'reject' && !comment.value.trim())

function handleApprove() {
  actionType.value = 'approve'
  showCommentInput.value = true
  ccList.value = props.flow?.ccList ? [...props.flow.ccList] : []
}

function handleReject() {
  actionType.value = 'reject'
  showCommentInput.value = true
  ccList.value = props.flow?.ccList ? [...props.flow.ccList] : []
}

function submitAction() {
  if (actionType.value === 'reject' && !comment.value.trim()) {
    return
  }
  if (actionType.value === 'approve') {
    emit('approve', comment.value, ccList.value)
  } else {
    emit('reject', comment.value, ccList.value)
  }
  comment.value = ''
  showCommentInput.value = false
  ccList.value = []
}

function addCcPerson() {
  if (newCcName.value.trim()) {
    ccList.value.push({
      name: newCcName.value.trim(),
      department: newCcDepartment.value.trim() || undefined,
    })
    newCcName.value = ''
    newCcDepartment.value = ''
  }
}

function removeCcPerson(index: number) {
  ccList.value.splice(index, 1)
}

function close() {
  emit('update:open', false)
  emit('close')
  comment.value = ''
  showCommentInput.value = false
  ccList.value = []
  showCcInput.value = false
  newCcName.value = ''
  newCcDepartment.value = ''
}

const statusIcon = {
  pending: Clock,
  approved: CheckCircle2,
  rejected: XCircle,
  cancelled: XCircle,
}

const statusColor = {
  pending: 'text-yellow-600 dark:text-yellow-400',
  approved: 'text-green-600 dark:text-green-400',
  rejected: 'text-red-600 dark:text-red-400',
  cancelled: 'text-gray-600 dark:text-gray-400',
}
</script>

<template>
  <div
    v-if="open && flow"
    class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm"
    @click.self="close"
  >
    <div class="w-[600px] max-h-[80vh] bg-background border border-border rounded-lg shadow-xl flex flex-col">
      <!-- 头部 -->
      <div class="flex items-center justify-between p-4 border-b border-border">
        <div>
          <h2 class="text-lg font-semibold text-foreground">审批流程</h2>
          <p class="text-sm text-muted-foreground">{{ flow.title }}</p>
        </div>
        <div class="flex items-center gap-2">
          <StatusBadge :status="flow.status" size="md" />
          <button
            class="inline-flex items-center justify-center rounded-md h-8 w-8 text-muted-foreground hover:text-foreground hover:bg-accent transition-colors"
            @click="close"
          >
            <XCircle class="h-5 w-5" />
          </button>
        </div>
      </div>

      <!-- 业务数据（Hover 显示） -->
      <div v-if="flow.businessData" class="p-4 border-b border-border">
        <div class="relative group">
          <button class="inline-flex items-center gap-2 text-sm text-primary hover:underline">
            <Info class="h-4 w-4" />
            查看业务数据
          </button>

          <!-- Hover 显示业务数据 -->
          <div class="hidden group-hover:block absolute top-full left-0 mt-2 w-80 bg-background border border-border rounded-lg shadow-xl z-50 p-4">
            <!-- 订单类型（综合办只能看到客户名称） -->
            <div v-if="flow.businessData.type === 'order'" class="space-y-2">
              <h3 class="text-sm font-semibold text-foreground">订单信息</h3>
              <div class="space-y-1 text-sm">
                <div v-if="flow.businessData.orderNumber" class="flex items-center justify-between">
                  <span class="text-muted-foreground">订单编号：</span>
                  <span class="font-medium text-foreground">{{ flow.businessData.orderNumber }}</span>
                </div>
                <div v-if="flow.businessData.customerName" class="flex items-center justify-between">
                  <span class="text-muted-foreground">客户名称：</span>
                  <span class="font-medium text-foreground">{{ flow.businessData.customerName }}</span>
                </div>
                <div v-if="flow.businessData.totalAmount" class="flex items-center justify-between">
                  <span class="text-muted-foreground">订单金额：</span>
                  <span class="font-semibold text-primary">¥{{ flow.businessData.totalAmount.toFixed(2) }}</span>
                </div>
              </div>

              <!-- 修改内容对比 -->
              <div v-if="flow.businessData.modifications && flow.businessData.modifications.length > 0" class="mt-3 pt-3 border-t border-border">
                <h3 class="text-sm font-semibold text-foreground mb-2">修改内容</h3>
                <div class="space-y-2">
                  <div
                    v-for="(mod, index) in flow.businessData.modifications"
                    :key="index"
                    class="p-2 rounded-md bg-muted/20 text-xs"
                  >
                    <div class="font-medium text-foreground mb-1">{{ mod.field }}</div>
                    <div class="flex items-center gap-2 text-muted-foreground">
                      <span class="line-through">{{ mod.before }}</span>
                      <span>→</span>
                      <span class="text-primary">{{ mod.after }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- 客户信息修改类型 -->
            <div v-else-if="flow.businessData.type === 'customer_info'" class="space-y-2">
              <h3 class="text-sm font-semibold text-foreground">客户信息修改</h3>
              <div class="space-y-1 text-sm">
                <div v-if="flow.businessData.customerName" class="flex items-center justify-between">
                  <span class="text-muted-foreground">客户名称：</span>
                  <span class="font-medium text-foreground">{{ flow.businessData.customerName }}</span>
                </div>
              </div>

              <!-- 修改内容对比 -->
              <div v-if="flow.businessData.modifications && flow.businessData.modifications.length > 0" class="mt-3 pt-3 border-t border-border">
                <h3 class="text-sm font-semibold text-foreground mb-2">修改内容</h3>
                <div class="space-y-2">
                  <div
                    v-for="(mod, index) in flow.businessData.modifications"
                    :key="index"
                    class="p-2 rounded-md bg-muted/20 text-xs"
                  >
                    <div class="font-medium text-foreground mb-1">{{ mod.field }}</div>
                    <div class="flex items-center gap-2 text-muted-foreground">
                      <span class="line-through">{{ mod.before }}</span>
                      <span>→</span>
                      <span class="text-primary">{{ mod.after }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- 采购类型 -->
            <div v-else-if="flow.businessData.type === 'purchase'" class="space-y-2">
              <h3 class="text-sm font-semibold text-foreground">预采购订单项</h3>
              <div class="space-y-2 max-h-48 overflow-y-auto">
                <div
                  v-for="(item, index) in flow.businessData.items"
                  :key="index"
                  class="p-2 rounded-md border border-border text-xs"
                >
                  <div class="flex items-center justify-between mb-1">
                    <span class="font-medium text-foreground">{{ item.productName }}</span>
                    <span class="font-semibold text-foreground">¥{{ item.amount.toFixed(2) }}</span>
                  </div>
                  <div class="flex items-center gap-3 text-muted-foreground">
                    <span>数量：{{ item.quantity }}</span>
                    <span>单位：{{ item.unit }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 提交人信息 -->
      <div v-if="flow.submitter" class="p-4 border-b border-border bg-muted/20">
        <div class="flex items-start gap-2">
          <Info class="h-4 w-4 text-muted-foreground mt-0.5" />
          <div class="flex-1">
            <div class="flex items-center gap-2">
              <span class="text-sm font-medium text-foreground">{{ flow.submitter }}</span>
              <span class="text-xs text-muted-foreground">提交申请</span>
            </div>
            <p v-if="flow.submitComment" class="text-sm text-muted-foreground mt-1">
              {{ flow.submitComment }}
            </p>
          </div>
        </div>
      </div>

      <!-- 进度条 -->
      <div class="p-4 border-b border-border">
        <div class="flex items-center justify-between mb-2">
          <span class="text-sm text-muted-foreground">审批进度</span>
          <span class="text-sm font-medium text-foreground">
            {{ approvedSteps }} / {{ flow.totalSteps }}（已通过/总步骤）
          </span>
        </div>
        <div class="h-2 rounded-full bg-muted overflow-hidden">
          <div
            class="h-full bg-primary transition-all duration-300"
            :style="{ width: `${(approvedSteps / flow.totalSteps) * 100}%` }"
          />
        </div>
      </div>

      <!-- 审批记录列表 -->
      <div class="flex-1 overflow-y-auto p-4">
        <div class="space-y-3">
          <div
            v-for="(record, index) in flow.records"
            :key="record.id"
            class="rounded-lg border border-border p-3"
            :class="record.status === 'pending' ? 'bg-yellow-50/50 dark:bg-yellow-900/10' : ''"
          >
            <div class="flex items-start gap-3">
              <!-- 状态图标 -->
              <div class="flex-shrink-0 mt-0.5">
                <component
                  :is="statusIcon[record.status]"
                  class="h-5 w-5"
                  :class="statusColor[record.status]"
                />
              </div>

              <!-- 内容 -->
              <div class="flex-1">
                <div class="flex items-center justify-between">
                  <div class="flex items-center gap-2">
                    <span class="text-sm font-medium text-foreground">{{ record.approver }}</span>
                    <span class="text-xs text-muted-foreground">{{ record.department }}</span>
                  </div>
                  <StatusBadge :status="record.status" />
                </div>

                <p v-if="record.comment" class="text-sm text-muted-foreground mt-1">
                  {{ record.comment }}
                </p>

                <div class="flex items-center gap-1 mt-1 text-xs text-muted-foreground">
                  <Clock class="h-3 w-3" />
                  <span>{{ record.timestamp }}</span>
                </div>
              </div>

              <!-- 步骤编号 -->
              <div class="flex-shrink-0">
                <span class="text-xs text-muted-foreground">步骤 {{ index + 1 }}</span>
              </div>
            </div>
          </div>
        </div>

        <div v-if="flow.records.length === 0" class="text-center py-8">
          <Clock class="h-12 w-12 mx-auto text-muted-foreground mb-2" />
          <p class="text-muted-foreground">暂无审批记录</p>
        </div>
      </div>

      <!-- 操作区域 -->
      <div v-if="flow.status === 'pending'" class="border-t border-border p-4">
        <!-- 评论输入框 -->
        <div v-if="showCommentInput" class="mb-3">
          <div class="flex items-start gap-2">
            <MessageSquare class="h-5 w-5 text-muted-foreground mt-2" />
            <div class="flex-1">
              <textarea
                v-model="comment"
                rows="3"
                :placeholder="actionType === 'approve' ? '输入审批意见（可选）' : '输入驳回原因（必填）'"
                class="w-full rounded-md border border-input bg-background px-3 py-2 text-sm placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring"
                :class="{ 'border-destructive': actionType === 'reject' && !comment.trim() }"
              />
              <p v-if="actionType === 'reject' && !comment.trim()" class="text-xs text-destructive mt-1">
                驳回原因不能为空
              </p>

              <!-- 抄送人 -->
              <div class="mt-3 pt-3 border-t border-border">
                <div class="flex items-center justify-between mb-2">
                  <span class="text-sm font-medium text-foreground">抄送人</span>
                  <button
                    class="text-xs text-primary hover:underline"
                    @click="showCcInput = !showCcInput"
                  >
                    {{ showCcInput ? '收起' : '添加' }}
                  </button>
                </div>

                <!-- 已添加的抄送人 -->
                <div v-if="ccList.length > 0" class="space-y-1 mb-2">
                  <div
                    v-for="(person, index) in ccList"
                    :key="index"
                    class="inline-flex items-center gap-1 px-2 py-1 rounded-md bg-muted/50 text-xs mr-1"
                  >
                    <User class="h-3 w-3" />
                    <span>{{ person.name }}</span>
                    <span v-if="person.department" class="text-muted-foreground">({{ person.department }})</span>
                    <button
                      class="text-muted-foreground hover:text-foreground"
                      @click="removeCcPerson(index)"
                    >
                      <X class="h-3 w-3" />
                    </button>
                  </div>
                </div>

                <!-- 添加抄送人输入框 -->
                <div v-if="showCcInput" class="flex items-center gap-2">
                  <input
                    v-model="newCcName"
                    type="text"
                    placeholder="姓名"
                    class="flex-1 rounded-md border border-input bg-background px-2 py-1 text-xs placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring"
                  />
                  <input
                    v-model="newCcDepartment"
                    type="text"
                    placeholder="部门（可选）"
                    class="flex-1 rounded-md border border-input bg-background px-2 py-1 text-xs placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring"
                  />
                  <button
                    class="px-2 py-1 text-xs font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
                    @click="addCcPerson"
                  >
                    <Plus class="h-3 w-3" />
                  </button>
                </div>
              </div>
            </div>
          </div>
          <div class="flex items-center justify-end gap-2 mt-2">
            <button
              class="px-3 py-1.5 text-xs font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
              @click="showCommentInput = false"
            >
              取消
            </button>
            <button
              class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
              :disabled="isRejectCommentRequired"
              @click="submitAction"
            >
              <Send class="h-3.5 w-3.5" />
              提交
            </button>
          </div>
        </div>

        <!-- 操作按钮 -->
        <div v-else class="flex items-center justify-end gap-2">
          <button
            class="inline-flex items-center gap-1.5 px-4 py-2 text-sm font-medium rounded-md bg-destructive text-destructive-foreground hover:bg-destructive/90 transition-colors"
            @click="handleReject"
          >
            <XCircle class="h-4 w-4" />
            驳回
          </button>
          <button
            class="inline-flex items-center gap-1.5 px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="handleApprove"
          >
            <CheckCircle2 class="h-4 w-4" />
            通过
          </button>
        </div>
      </div>

      <!-- 已完成状态 -->
      <div v-else class="border-t border-border p-4">
        <div class="flex items-center justify-center gap-2 text-sm text-muted-foreground">
          <CheckCircle2 v-if="flow.status === 'approved'" class="h-5 w-5 text-green-500" />
          <XCircle v-else class="h-5 w-5 text-red-500" />
          <span>
            {{ flow.status === 'approved' ? '审批已通过' : flow.status === 'rejected' ? '审批已驳回' : '审批已取消' }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>
