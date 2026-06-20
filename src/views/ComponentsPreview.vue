<script setup lang="ts">
import { ref } from 'vue'

// ========== 基础组件（被其他组件依赖） ==========
import Avatar from '@/components/business/Avatar.vue'
import Badge from '@/components/business/Badge.vue'
import Breadcrumb from '@/components/business/Breadcrumb.vue'
import DataTable from '@/components/business/DataTable.vue'
import Drawer from '@/components/business/Drawer.vue'
import DropdownMenu from '@/components/business/DropdownMenu.vue'
import EmptyState from '@/components/business/EmptyState.vue'
import Pagination from '@/components/business/Pagination.vue'
import StatusBadge from '@/components/business/StatusBadge.vue'
import UserSelect from '@/components/business/UserSelect.vue'

// ========== 样式参考组件 ==========
import ApprovalConfig from '@/components/business/ApprovalConfig.vue'
import ApprovalFlow from '@/components/business/ApprovalFlow.vue'
import ApprovalTimeline from '@/components/business/ApprovalTimeline.vue'
import CardHeader from '@/components/business/CardHeader.vue'
import DualItemCard from '@/components/business/DualItemCard.vue'
import FileUpload from '@/components/business/FileUpload.vue'
import InvoiceProcess from '@/components/business/InvoiceProcess.vue'
import LoginLog from '@/components/business/LoginLog.vue'
import Modal from '@/components/business/Modal.vue'
import NotificationCard from '@/components/business/NotificationCard.vue'
import NotificationList from '@/components/business/NotificationList.vue'
import OperationLog from '@/components/business/OperationLog.vue'
import OrderItemCard from '@/components/business/OrderItemCard.vue'
import PaymentCard from '@/components/business/PaymentCard.vue'
import PermissionConfig from '@/components/business/PermissionConfig.vue'
import SearchFilterBar from '@/components/business/SearchFilterBar.vue'
import SelectionMode from '@/components/business/SelectionMode.vue'
import Toast from '@/components/business/Toast.vue'
import VerifyCodeInput from '@/components/business/VerifyCodeInput.vue'

// ========== 控制开关 ==========
const showApprovalFlow = ref(false)
const showDrawer = ref(false)
const showInvoiceProcess = ref(false)
const showModal = ref(false)
const showUserSelect = ref(false)

// ========== Mock 数据 ==========

// 基础组件
const dropdownItems = [{ key: 'edit', label: '编辑' }, { key: 'delete', label: '删除', danger: true }]
const tableColumns = [{ key: 'id', title: 'ID' }, { key: 'name', title: '名称' }, { key: 'amount', title: '金额' }]
const tableData = [{ id: 1, name: '缠绕垫片 DN50', amount: '¥8,500' }, { id: 2, name: '四氟垫片 DN100', amount: '¥12,000' }]
const selectUsers = [{ id: 'u1', name: '张三', employeeNumber: 'EMP001', department: '销售部', role: 'sales', avatar: '', permissions: [] }]

// 审批
const approvalFlows = [{
  id: '1', name: '报价单审批', type: 'quotation' as const, enabled: true,
  steps: [{ id: 's1', approverId: 'u1', approverName: '张三', department: '销售部', order: 1 }],
}]
const approvalFlowData = {
  id: 'af1', title: '报价单审批', status: 'pending' as const,
  currentStep: 1, totalSteps: 2, submitter: '王五', submitComment: '请审批',
  records: [{ id: 'r1', approver: '张三', department: '销售部', status: 'approved' as const, comment: '同意', timestamp: '2026-06-15 10:00' }],
}
const approvalNode = { id: 'n1', title: '销售经理审批', approver: '张三', department: '销售部', status: 'approved' as const, comment: '同意', timestamp: '2026-06-15 10:30' }

// 订单
const dualItem = { id: 'oi1', number: 'PI-001', productName: '缠绕垫片', specification: 'DN50 PN16', quantity: 100, unitPrice: 85, amount: 8500, status: 'pending', note: '客户指定进口材料' }
const dualItems = [{ id: 'ci1', number: 'CI-001', productName: '缠绕垫片（国产）', specification: 'DN50 PN16', quantity: 100, unitPrice: 65, amount: 6500, status: 'pending', note: '' }]
const orderItem = { id: 'oi2', number: 'PI-002', productName: '四氟垫片', specification: 'DN100 CL150', quantity: 50, unitPrice: 120, amount: 6000, status: 'completed' as const }

// 发票/付款
const invoiceItems = [{ id: 'i1', orderNumber: 'ORD-20260610-001', customerName: '洛阳中信重工', productName: '缠绕垫片', specification: 'DN50 PN16', quantity: 100, unitPrice: 85, amount: 8500 }]
const paymentData = { id: 'pay1', paymentNumber: 'PAY-20260615-001', orderNumber: 'ORD-20260610-001', customerName: '洛阳中信重工', amount: 125000, paidAmount: 85000, paymentDate: '2026-06-15', dueDate: '2026-07-15', status: 'partial' as const, method: 'bank_transfer' as const }

// 通知/日志
const notifications = [{ id: 'n1', type: 'approval', title: '报价单待审批', description: 'QT-20260615-001 等待审批', time: '10分钟前', isRead: false }]
const operationRecords = [{ id: 'op1', time: '2026-06-15 10:30', user: '张三', action: '创建报价单', detail: 'QT-20260615-001' }]
const loginLogs = [{ id: 'll1', time: '2026-06-15 09:00', user: '张三', ip: '192.168.1.100', device: 'Chrome / Windows', status: 'success' as const }]

// 权限
const permissions = [{ id: 'qt-view', name: '查看报价单', description: '查看报价单列表和详情', module: '报价单', enabled: true }, { id: 'qt-create', name: '创建报价单', description: '创建新的报价单', module: '报价单', enabled: true }]

// 筛选
const searchFilters = [{ key: 'status', label: '状态', options: [{ value: 'all', label: '全部' }, { value: 'active', label: '活跃' }] }]
const selectionItems = [{ id: '1', label: '报价单 QT-001', sublabel: '洛阳中信重工' }, { id: '2', label: '报价单 QT-002', sublabel: '郑州煤矿机械' }]
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-6xl mx-auto space-y-6">
      <div class="space-y-2 sticky top-0 bg-background py-4 z-10 border-b border-border">
        <h1 class="text-2xl font-bold text-foreground">组件评审（29 个）</h1>
        <p class="text-muted-foreground">基础组件 10 个（被依赖）+ 样式参考组件 19 个。每个都标注了用途。</p>
      </div>

      <!-- ==================== 基础组件 ==================== -->
      <div class="space-y-2">
        <h2 class="text-lg font-semibold text-foreground border-l-4 border-primary pl-3">基础组件（10 个）</h2>
        <p class="text-sm text-muted-foreground">被其他组件依赖，不可删除</p>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>Avatar</strong> — 用户头像，支持 initials 回退、多种尺寸</p>
          <div class="flex items-center gap-3"><Avatar name="张三" size="sm" /><Avatar name="李四" size="md" /><Avatar name="王五" size="lg" /></div>
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>Badge</strong> — 数字徽标，支持上限、dot 模式</p>
          <div class="flex items-center gap-4"><Badge :count="5" /><Badge :count="99" :max-count="99" /><Badge dot /></div>
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>Breadcrumb</strong> — 面包屑导航</p>
          <Breadcrumb :items="[{ label: '首页', href: '#' }, { label: '报价单' }, { label: 'QT-001' }]" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>DataTable</strong> — 通用数据表格（排序/分页/搜索/选择）</p>
          <DataTable :columns="tableColumns" :data="tableData" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>Drawer</strong> — 侧滑抽屉，左右分栏布局</p>
          <button class="text-sm text-primary underline" @click="showDrawer = true">打开抽屉</button>
          <Drawer v-model:open="showDrawer" title="详情">
            <template #list><p class="text-sm text-muted-foreground p-4">左侧内容区</p></template>
            <template #detail><p class="text-sm text-muted-foreground p-4">右侧操作区</p></template>
          </Drawer>
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>DropdownMenu</strong> — 下拉菜单</p>
          <DropdownMenu :items="dropdownItems" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>EmptyState</strong> — 空状态占位</p>
          <EmptyState title="暂无数据" description="还没有任何记录" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>Pagination</strong> — 分页控件</p>
          <Pagination :current="1" :total="100" :page-size="10" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>StatusBadge</strong> — 状态徽章，25+ 种预设状态</p>
          <div class="flex flex-wrap gap-2"><StatusBadge status="draft" /><StatusBadge status="pending" /><StatusBadge status="approved" /><StatusBadge status="completed" /><StatusBadge status="rejected" /></div>
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-1"><strong>UserSelect</strong> — 用户选择弹窗</p>
          <button class="text-sm text-primary underline" @click="showUserSelect = true">打开用户选择</button>
          <UserSelect :users="selectUsers" :open="showUserSelect" @update:open="showUserSelect = $event" />
        </div>
      </div>

      <!-- ==================== 样式参考组件 ==================== -->
      <div class="space-y-2 mt-8">
        <h2 class="text-lg font-semibold text-foreground border-l-4 border-primary pl-3">样式参考组件（19 个）</h2>
        <p class="text-sm text-muted-foreground">展示如何将 shadcn 组合为业务 UI，开发时参考结构模式，不要直接复用 props</p>
      </div>

      <div class="grid grid-cols-1 gap-4">
        <!-- 审批类 -->
        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-2"><strong>ApprovalConfig</strong> — 多步骤审批流配置面板。参考：流程配置 UI 的结构模式</p>
          <ApprovalConfig :flows="approvalFlows" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-2"><strong>ApprovalFlow</strong> — 审批进度抽屉（步骤条 + 通过/驳回操作）。参考：步骤进度 + 操作按钮的抽屉布局</p>
          <button class="text-sm text-primary underline mb-2" @click="showApprovalFlow = true">点击打开</button>
          <ApprovalFlow :flow="approvalFlowData" :open="showApprovalFlow" @update:open="showApprovalFlow = $event" />
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-2"><strong>ApprovalTimeline</strong> — 审批节点时间线。参考：时间线节点的视觉模式</p>
          <ApprovalTimeline :node="approvalNode" />
        </div>

        <!-- 卡片/列表类 -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="rounded-lg border border-border overflow-hidden">
            <p class="text-xs text-muted-foreground m-2"><strong>CardHeader</strong> — 卡片头部标准模式（标题+副标题+状态+菜单）</p>
            <CardHeader title="报价单" subtitle="QT-20260615-001" status="confirmed" :menu-items="dropdownItems" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>DualItemCard</strong> — 阴阳项目卡片。参考：一对多/多对一关系展示模式</p>
            <DualItemCard :item="dualItem" :dual-items="dualItems" :is-duality="true" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>OrderItemCard</strong> — 订单项卡片。参考：列表项卡片模式（状态+操作）</p>
            <OrderItemCard :item="orderItem" :show-actions="true" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>PaymentCard</strong> — 付款记录卡片。参考：金额展示卡片模式</p>
            <PaymentCard :payment="paymentData" />
          </div>
        </div>

        <!-- 工作流/面板类 -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>InvoiceProcess</strong> — 发票处理面板。参考：选择+处理的工作流面板</p>
            <button class="text-sm text-primary underline mb-2" @click="showInvoiceProcess = true">点击打开</button>
            <InvoiceProcess :items="invoiceItems" :open="showInvoiceProcess" @update:open="showInvoiceProcess = $event" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>PermissionConfig</strong> — 权限矩阵。参考：Checkbox 网格权限配置面板</p>
            <PermissionConfig :permissions="permissions" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>SearchFilterBar</strong> — 搜索栏+筛选+新建按钮。参考：列表页顶部工具栏模式</p>
            <SearchFilterBar :filters="searchFilters" search-placeholder="搜索..." show-create-button create-button-text="新建" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>SelectionMode</strong> — 批量选择模式。参考：全选+批量操作交互模式</p>
            <SelectionMode :items="selectionItems" :selectable="true" />
          </div>
        </div>

        <!-- 通知/日志类 -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>NotificationCard</strong> — 单条通知卡片。参考：通知卡片视觉模式（图标+已读状态）</p>
            <NotificationCard id="n1" type="approval" title="报价单待审批" description="QT-20260615-001 等待审批" time="10分钟前" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>NotificationList</strong> — 通知列表。参考：通知列表布局（分组+查看全部）</p>
            <NotificationList :notifications="notifications" title="系统通知" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>OperationLog</strong> — 操作日志。参考：操作记录时间线展示模式</p>
            <OperationLog :records="operationRecords" title="操作日志" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>LoginLog</strong> — 登录日志表格。参考：日志类页面的标准模式（筛选+搜索+表格）</p>
            <LoginLog :logs="loginLogs" />
          </div>
        </div>

        <!-- 其他 -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>FileUpload</strong> — 拖拽上传区。参考：文件上传交互模式</p>
            <FileUpload accept=".pdf,.xlsx" :max-size="10" />
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>Modal</strong> — 通用弹窗。参考：弹窗标准模式</p>
            <button class="text-sm text-primary underline" @click="showModal = true">打开弹窗</button>
            <Modal v-model:open="showModal" title="弹窗标题" width="md"><p class="text-sm text-muted-foreground">弹窗内容</p></Modal>
          </div>

          <div class="rounded-lg border border-border p-4">
            <p class="text-xs text-muted-foreground mb-2"><strong>VerifyCodeInput</strong> — 验证码输入。参考：验证码交互模式（倒计时+发送）</p>
            <VerifyCodeInput :length="6" :cooldown="60" />
          </div>
        </div>

        <div class="rounded-lg border border-border p-4">
          <p class="text-xs text-muted-foreground mb-2"><strong>Toast</strong> — Toast 通知（编程式调用，无法在模板中直接预览）</p>
          <p class="text-sm text-muted-foreground">通过 `useToast()` 调用，支持 success/error/warning/info 类型</p>
        </div>
      </div>

      <!-- 底部统计 -->
      <div class="rounded-lg border border-border p-6 bg-muted/20">
        <h3 class="text-sm font-semibold text-foreground mb-3">最终清单</h3>
        <div class="text-sm text-muted-foreground space-y-1">
          <p><strong>基础组件（10 个）：</strong>Avatar、Badge、Breadcrumb、DataTable、Drawer、DropdownMenu、EmptyState、Pagination、StatusBadge、UserSelect</p>
          <p><strong>样式参考（19 个）：</strong>ApprovalConfig、ApprovalFlow、ApprovalTimeline、CardHeader、DualItemCard、FileUpload、InvoiceProcess、LoginLog、Modal、NotificationCard、NotificationList、OperationLog、OrderItemCard、PaymentCard、PermissionConfig、SearchFilterBar、SelectionMode、Toast、VerifyCodeInput</p>
          <p class="pt-2 font-medium text-foreground">总计：29 个</p>
        </div>
      </div>
    </div>
  </div>
</template>
