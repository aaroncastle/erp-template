<script setup lang="ts">
import { ref, computed } from 'vue'
import { Shield, Save } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Checkbox } from '@/components/ui/checkbox'

export interface Permission {
  id: string
  name: string
  description: string
  module: string
  enabled: boolean
}

interface Props {
  permissions?: Permission[]
}

const props = withDefaults(defineProps<Props>(), {
  permissions: () => [
    // 报价单模块
    { id: 'qt-view', name: '查看报价单', description: '查看报价单列表和详情', module: '报价单', enabled: true },
    { id: 'qt-create', name: '创建报价单', description: '创建新的报价单', module: '报价单', enabled: true },
    { id: 'qt-edit', name: '编辑报价单', description: '编辑报价单信息', module: '报价单', enabled: true },
    { id: 'qt-delete', name: '删除报价单', description: '删除报价单', module: '报价单', enabled: false },
    { id: 'qt-submit', name: '提交报价单', description: '提交报价单审批', module: '报价单', enabled: true },

    // 订单模块
    { id: 'ord-view', name: '查看订单', description: '查看订单列表和详情', module: '订单', enabled: true },
    { id: 'ord-create', name: '创建订单', description: '创建新的订单', module: '订单', enabled: true },
    { id: 'ord-edit', name: '编辑订单', description: '编辑订单信息', module: '订单', enabled: true },
    { id: 'ord-delete', name: '删除订单', description: '删除订单', module: '订单', enabled: false },
    { id: 'ord-ship', name: '发货', description: '执行发货操作', module: '订单', enabled: false },

    // 开票模块
    { id: 'inv-view', name: '查看发票', description: '查看发票列表和详情', module: '开票', enabled: true },
    { id: 'inv-create', name: '申请开票', description: '申请开具发票', module: '开票', enabled: true },
    { id: 'inv-issue', name: '开具发票', description: '实际开具发票', module: '开票', enabled: false },

    // 支付模块
    { id: 'pay-view', name: '查看收款', description: '查看收款记录', module: '支付', enabled: true },
    { id: 'pay-record', name: '记录收款', description: '记录收款信息', module: '支付', enabled: true },

    // 仓储模块
    { id: 'wh-view', name: '查看仓储', description: '查看仓储记录', module: '仓储', enabled: true },
    { id: 'wh-manage', name: '管理仓储', description: '执行入库/出库操作', module: '仓储', enabled: false },

    // 系统管理
    { id: 'sys-user', name: '用户管理', description: '管理用户账号', module: '系统', enabled: false },
    { id: 'sys-config', name: '系统配置', description: '配置系统参数', module: '系统', enabled: false },
  ],
})

const emit = defineEmits<{
  (e: 'save', permissions: Permission[]): void
}>()

// 本地权限列表
const localPermissions = ref<Permission[]>([...props.permissions])

// 按模块分组
const groupedPermissions = computed(() => {
  const groups: Record<string, Permission[]> = {}
  localPermissions.value.forEach(perm => {
    if (!groups[perm.module]) {
      groups[perm.module] = []
    }
    groups[perm.module].push(perm)
  })
  return groups
})

// 切换权限
function togglePermission(permissionId: string) {
  const perm = localPermissions.value.find(p => p.id === permissionId)
  if (perm) {
    perm.enabled = !perm.enabled
  }
}

// 保存权限
function handleSave() {
  emit('save', localPermissions.value)
}
</script>

<template>
  <Card>
    <CardHeader>
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <Shield class="h-5 w-5 text-primary" />
          <CardTitle>权限配置</CardTitle>
        </div>
        <Button @click="handleSave">
          <Save class="h-4 w-4 mr-2" />
          保存
        </Button>
      </div>
    </CardHeader>
    <CardContent>
      <div class="space-y-6">
        <div
          v-for="(permissions, module) in groupedPermissions"
          :key="module"
          class="space-y-3"
        >
          <h3 class="text-sm font-semibold text-foreground border-b border-border pb-2">
            {{ module }}
          </h3>
          <div class="space-y-2">
            <div
              v-for="perm in permissions"
              :key="perm.id"
              class="flex items-start gap-3 p-3 rounded-lg border border-border hover:bg-muted/30 transition-colors"
            >
              <Checkbox
                :checked="perm.enabled"
                @update:checked="togglePermission(perm.id)"
              />
              <div class="flex-1">
                <div class="flex items-center gap-2">
                  <span class="text-sm font-medium text-foreground">{{ perm.name }}</span>
                  <span class="text-xs text-muted-foreground">{{ perm.id }}</span>
                </div>
                <p class="text-xs text-muted-foreground mt-1">{{ perm.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </CardContent>
  </Card>
</template>
