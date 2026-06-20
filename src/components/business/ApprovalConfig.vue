<script setup lang="ts">
import { ref } from 'vue'
import { GitBranch, Plus, Edit, Trash2, Save, User } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Checkbox } from '@/components/ui/checkbox'
import UserSelect from './UserSelect.vue'

export interface ApprovalStep {
  id: string
  approverId: string
  approverName: string
  department?: string
  order: number
}

export interface ApprovalFlow {
  id: string
  name: string
  type: 'quotation' | 'order' | 'invoice' | 'payment'
  steps: ApprovalStep[]
  defaultCcList?: CcPerson[]
  enabled: boolean
}

export interface CcPerson {
  id: string
  name: string
  department?: string
}

interface Props {
  flows?: ApprovalFlow[]
}

const props = withDefaults(defineProps<Props>(), {
  flows: () => [
    {
      id: '1',
      name: '报价单审批流程',
      type: 'quotation',
      enabled: true,
      steps: [
        { id: '1', approverId: 'user1', approverName: '李四', department: '综合办', order: 1 },
        { id: '2', approverId: 'user2', approverName: '王五', department: '财务部', order: 2 },
      ],
    },
    {
      id: '2',
      name: '订单审批流程',
      type: 'order',
      enabled: true,
      steps: [
        { id: '3', approverId: 'user3', approverName: '赵六', department: '综合办', order: 1 },
      ],
    },
  ],
})

const emit = defineEmits<{
  (e: 'save', flows: ApprovalFlow[]): void
}>()

const localFlows = ref<ApprovalFlow[]>([...props.flows])
const showUserSelect = ref(false)
const showCcUserSelect = ref(false)
const currentFlowId = ref<string>('')
const isAddingCc = ref(false)

const typeLabels = {
  quotation: '报价单',
  order: '订单',
  invoice: '发票',
  payment: '支付',
}

// 添加审批流
function addFlow() {
  const newFlow: ApprovalFlow = {
    id: Date.now().toString(),
    name: '新审批流程',
    type: 'quotation',
    enabled: true,
    steps: [],
  }
  localFlows.value.push(newFlow)
}

// 删除审批流
function removeFlow(flowId: string) {
  localFlows.value = localFlows.value.filter(f => f.id !== flowId)
}

// 添加审批步骤
function addStep(flowId: string) {
  currentFlowId.value = flowId
  showUserSelect.value = true
}

// 选择审批人
function handleSelectUser(user: any) {
  const flow = localFlows.value.find(f => f.id === currentFlowId.value)
  if (flow) {
    const newStep: ApprovalStep = {
      id: Date.now().toString(),
      approverId: user.id,
      approverName: user.name,
      department: user.department,
      order: flow.steps.length + 1,
    }
    flow.steps.push(newStep)
  }
  showUserSelect.value = false
}

// 删除审批步骤
function removeStep(flowId: string, stepId: string) {
  const flow = localFlows.value.find(f => f.id === flowId)
  if (flow) {
    flow.steps = flow.steps.filter(s => s.id !== stepId)
    // 重新排序
    flow.steps.forEach((step, index) => {
      step.order = index + 1
    })
  }
}

// 添加抄送人
function addCcPerson(flowId: string) {
  currentFlowId.value = flowId
  isAddingCc.value = true
  showCcUserSelect.value = true
}

// 选择抄送人
function handleSelectCcUser(user: any) {
  const flow = localFlows.value.find(f => f.id === currentFlowId.value)
  if (flow) {
    if (!flow.defaultCcList) {
      flow.defaultCcList = []
    }
    const newCc: CcPerson = {
      id: user.id,
      name: user.name,
      department: user.department,
    }
    flow.defaultCcList.push(newCc)
  }
  showCcUserSelect.value = false
  isAddingCc.value = false
}

// 删除抄送人
function removeCcPerson(flowId: string, ccId: string) {
  const flow = localFlows.value.find(f => f.id === flowId)
  if (flow && flow.defaultCcList) {
    flow.defaultCcList = flow.defaultCcList.filter(cc => cc.id !== ccId)
  }
}

// 保存
function handleSave() {
  emit('save', localFlows.value)
}
</script>

<template>
  <Card>
    <CardHeader>
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <GitBranch class="h-5 w-5 text-primary" />
          <CardTitle>审批流程配置</CardTitle>
        </div>
        <div class="flex items-center gap-2">
          <Button variant="outline" @click="addFlow">
            <Plus class="h-4 w-4 mr-2" />
            添加流程
          </Button>
          <Button @click="handleSave">
            <Save class="h-4 w-4 mr-2" />
            保存
          </Button>
        </div>
      </div>
    </CardHeader>
    <CardContent>
      <div class="space-y-4">
        <div
          v-for="flow in localFlows"
          :key="flow.id"
          class="p-4 border border-border rounded-lg"
        >
          <div class="flex items-center justify-between mb-3">
            <div class="flex items-center gap-3">
              <Checkbox
                :checked="flow.enabled"
                @update:checked="flow.enabled = !flow.enabled"
              />
              <Input
                v-model="flow.name"
                class="max-w-xs"
                placeholder="流程名称"
              />
              <span class="text-sm text-muted-foreground">
                类型：{{ typeLabels[flow.type] }}
              </span>
            </div>
            <Button variant="ghost" size="sm" @click="removeFlow(flow.id)">
              <Trash2 class="h-4 w-4 text-destructive" />
            </Button>
          </div>

          <!-- 审批步骤 -->
          <div class="space-y-2 ml-8">
            <div
              v-for="step in flow.steps"
              :key="step.id"
              class="flex items-center justify-between p-2 rounded-md bg-muted/30"
            >
              <div class="flex items-center gap-2">
                <span class="text-xs text-muted-foreground">步骤 {{ step.order }}</span>
                <span class="text-sm font-medium text-foreground">{{ step.approverName }}</span>
                <span v-if="step.department" class="text-xs text-muted-foreground">
                  ({{ step.department }})
                </span>
              </div>
              <Button variant="ghost" size="sm" @click="removeStep(flow.id, step.id)">
                <Trash2 class="h-3 w-3 text-destructive" />
              </Button>
            </div>
            <Button variant="outline" size="sm" @click="addStep(flow.id)">
              <Plus class="h-3 w-3 mr-1" />
              添加审批人
            </Button>
          </div>

          <!-- 默认抄送人 -->
          <div class="space-y-2 ml-8 mt-4">
            <div class="flex items-center gap-2">
              <User class="h-4 w-4 text-muted-foreground" />
              <span class="text-sm font-medium text-foreground">默认抄送人</span>
            </div>
            <div
              v-for="cc in flow.defaultCcList"
              :key="cc.id"
              class="flex items-center justify-between p-2 rounded-md bg-blue-50 dark:bg-blue-900/20"
            >
              <div class="flex items-center gap-2">
                <User class="h-3 w-3 text-blue-600 dark:text-blue-400" />
                <span class="text-sm font-medium text-foreground">{{ cc.name }}</span>
                <span v-if="cc.department" class="text-xs text-muted-foreground">
                  ({{ cc.department }})
                </span>
              </div>
              <Button variant="ghost" size="sm" @click="removeCcPerson(flow.id, cc.id)">
                <Trash2 class="h-3 w-3 text-destructive" />
              </Button>
            </div>
            <Button variant="outline" size="sm" @click="addCcPerson(flow.id)">
              <Plus class="h-3 w-3 mr-1" />
              添加抄送人
            </Button>
          </div>
        </div>
      </div>
    </CardContent>

    <!-- 用户选择器（审批人） -->
    <UserSelect
      v-model:open="showUserSelect"
      :users="[]"
      @select="handleSelectUser"
    />

    <!-- 用户选择器（抄送人） -->
    <UserSelect
      v-model:open="showCcUserSelect"
      :users="[]"
      @select="handleSelectCcUser"
    />
  </Card>
</template>
