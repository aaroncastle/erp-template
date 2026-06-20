<script setup lang="ts">
import { ref, computed } from 'vue'
import Header from '@/components/layout/Header.vue'
import RightPanel from '@/components/layout/RightPanel.vue'
import DualItemCard from '@/components/business/DualItemCard.vue'
import Drawer from '@/components/business/Drawer.vue'
import StatusBadge from '@/components/business/StatusBadge.vue'
import SelectionMode from '@/components/business/SelectionMode.vue'
import Breadcrumb from '@/components/business/Breadcrumb.vue'
import InvoiceProcess from '@/components/business/InvoiceProcess.vue'
import ApprovalFlow from '@/components/business/ApprovalFlow.vue'
import { ChevronLeft, Filter, Package, List, CheckSquare, Square, Copy, Check as CheckIcon, MoreHorizontal } from '@lucide/vue'

type Module = 'quotation' | 'order'
type LeftMode = 'list' | 'notification-detail' | 'selection'

const currentModule = ref<Module>('order')
const leftMode = ref<LeftMode>('list')
const searchQuery = ref('')
const activeTab = ref('all')
const showDrawer = ref(false)
const isEditing = ref(false)
const selectedOrder = ref<any>(null)
const isSelectionMode = ref(false)
const showInvoiceProcess = ref(false)
const showApprovalFlow = ref(false)

// 模拟订单数据
const mockOrders = ref([
  {
    id: '1',
    number: 'ORD-20260615-003',
    customerName: '洛阳轴承集团',
    status: 'in_progress',
    createdAt: '2026-06-15',
    itemCount: 5,
    totalAmount: 28500,
    progress: '3/5 项',
    deliveryStatus: '待开票',
  },
  {
    id: '2',
    number: 'ORD-20260618-001',
    customerName: '洛阳能源密封件',
    status: 'pending',
    createdAt: '2026-06-18',
    itemCount: 5,
    totalAmount: 128000,
    progress: '',
    deliveryStatus: '',
  },
  {
    id: '3',
    number: 'ORD-20260610-002',
    customerName: '中石化洛阳分公司',
    status: 'completed',
    createdAt: '2026-06-10',
    itemCount: 3,
    totalAmount: 45000,
    progress: '3/3 项',
    deliveryStatus: '已完成',
  },
])

// 模拟阴阳项目数据
const mockDualItems = ref([
  {
    productionItem: {
      id: 'P-001',
      number: 'PROD-20260615-001',
      productName: '机械密封 A 型',
      specification: 'Φ100×Φ120×50',
      quantity: 10,
      unitPrice: 850.00,
      amount: 8500.00,
      status: 'in_progress',
    },
    customerItems: [
      {
        id: 'C-001',
        number: 'CUST-20260615-001',
        productName: '机械密封 A 型（客户指定）',
        specification: 'Φ100×Φ120×50',
        quantity: 10,
        unitPrice: 1200.00,
        amount: 12000.00,
        status: 'in_progress',
      },
    ],
  },
  {
    productionItem: {
      id: 'P-002',
      number: 'PROD-20260615-002',
      productName: '机械密封 B 型',
      specification: 'Φ120×Φ140×60',
      quantity: 8,
      unitPrice: 950.00,
      amount: 7600.00,
      status: 'pending_invoice',
    },
    customerItems: [
      {
        id: 'C-002',
        number: 'CUST-20260615-002',
        productName: '机械密封 B 型（客户指定）',
        specification: 'Φ120×Φ140×60',
        quantity: 8,
        unitPrice: 1400.00,
        amount: 11200.00,
        status: 'pending_invoice',
      },
      {
        id: 'C-003',
        number: 'CUST-20260615-003',
        productName: '机械密封 B 型（客户指定 2）',
        specification: 'Φ120×Φ140×60',
        quantity: 8,
        unitPrice: 1450.00,
        amount: 11600.00,
        status: 'pending_invoice',
      },
      {
        id: 'C-004',
        number: 'CUST-20260615-004',
        productName: '机械密封 B 型（客户指定 3）',
        specification: 'Φ120×Φ140×60',
        quantity: 8,
        unitPrice: 1500.00,
        amount: 12000.00,
        status: 'pending_invoice',
      },
      {
        id: 'C-005',
        number: 'CUST-20260615-005',
        productName: '机械密封 B 型（客户指定 4）',
        specification: 'Φ120×Φ140×60',
        quantity: 8,
        unitPrice: 1550.00,
        amount: 12400.00,
        status: 'pending_invoice',
      },
      {
        id: 'C-006',
        number: 'CUST-20260615-006',
        productName: '机械密封 B 型（客户指定 5）',
        specification: 'Φ120×Φ140×60',
        quantity: 8,
        unitPrice: 1600.00,
        amount: 12800.00,
        status: 'pending_invoice',
      },
    ],
  },
  {
    productionItem: {
      id: 'P-003',
      number: 'PROD-20260615-003',
      productName: '机械密封 C 型',
      specification: 'Φ80×Φ100×40',
      quantity: 15,
      unitPrice: 650.00,
      amount: 9750.00,
      status: 'pending',
    },
    customerItems: [],
  },
])

const filteredOrders = computed(() => {
  let orders = mockOrders.value

  // 按客户名称筛选
  if (searchQuery.value) {
    orders = orders.filter(order =>
      order.customerName.includes(searchQuery.value)
    )
  }

  // 按状态筛选
  if (activeTab.value !== 'all') {
    orders = orders.filter(order => order.status === activeTab.value)
  }

  return orders
})

const unreadNotificationCount = computed(() => 3)

function handleModuleChange(module: Module) {
  currentModule.value = module
  leftMode.value = 'list'
}

function handleTabChange(tab: string) {
  activeTab.value = tab
}

function handleSearch(query: string) {
  searchQuery.value = query
}

function handleOrderClick(order: any, editing = false) {
  selectedOrder.value = order
  isEditing.value = editing
  showDrawer.value = true
}

function handleEditOrder() {
  isEditing.value = true
}

function handleDualItemClick(item: any) {
  console.log('点击项目:', item)
}

function handleViewDual(item: any, dualItems: any[]) {
  console.log('查看指定项:', item, dualItems)
}

function handleNotificationClick(notification: any) {
  console.log('点击通知:', notification)
  leftMode.value = 'notification-detail'
}

function handleViewAllNotifications() {
  console.log('查看全部通知')
  // TODO: 打开通知抽屉
}

function handleCreateNew() {
  console.log('新建', currentModule.value)
  // TODO: 打开新建表单
}

function handleCopyNumber(number: string) {
  console.log('复制编号:', number)
}

function toggleSelectionMode() {
  isSelectionMode.value = !isSelectionMode.value
}

function handleSelectionChange(selectedIds: string[]) {
  console.log('选中项:', selectedIds)
}

function handleBatchDelete(selectedIds: string[]) {
  console.log('批量删除:', selectedIds)
}

function handleBatchInvoice(selectedIds: string[]) {
  console.log('联合开票:', selectedIds)
  showInvoiceProcess.value = true
}

function handleBatchExport(selectedIds: string[]) {
  console.log('批量导出:', selectedIds)
}

// 模拟开票数据
const mockInvoiceItems = ref([
  {
    id: '1',
    orderNumber: 'ORD-20260615-003',
    customerName: '洛阳轴承集团',
    productName: '机械密封 A 型',
    specification: 'Φ100×Φ120×50',
    quantity: 10,
    unitPrice: 850.00,
    amount: 8500.00,
  },
  {
    id: '2',
    orderNumber: 'ORD-20260615-003',
    customerName: '洛阳轴承集团',
    productName: '机械密封 B 型',
    specification: 'Φ120×Φ140×60',
    quantity: 8,
    unitPrice: 950.00,
    amount: 7600.00,
  },
])

// 模拟审批流程数据
const mockApprovalFlow = ref({
  id: '1',
  title: '订单 ORD-20260615-003 审批',
  status: 'pending' as const,
  records: [
    {
      id: '1',
      approver: '张三',
      department: '销售一部',
      status: 'approved' as const,
      comment: '同意',
      timestamp: '2026-06-18 10:30',
    },
    {
      id: '2',
      approver: '李四',
      department: '核算部',
      status: 'pending' as const,
      timestamp: '',
    },
    {
      id: '3',
      approver: '王五',
      department: '财务部',
      status: 'pending' as const,
      timestamp: '',
    },
  ],
  currentStep: 2,
  totalSteps: 3,
})

function handleInvoiceConfirm(items: any[]) {
  console.log('开票确认:', items)
  showInvoiceProcess.value = false
}

function handleApprovalApprove(comment: string) {
  console.log('审批通过:', comment)
  showApprovalFlow.value = false
}

function handleApprovalReject(comment: string) {
  console.log('审批驳回:', comment)
  showApprovalFlow.value = false
}
</script>

<template>
  <div class="min-h-screen bg-background">
    <!-- 顶部导航栏 -->
    <Header
      @user-menu-click="console.log('用户菜单')"
    />

    <!-- 主内容区 -->
    <div class="grid grid-cols-[1fr_320px] gap-6 p-6">
      <!-- 左侧主区域 -->
      <main class="min-w-0">
        <!-- 面包屑导航 -->
        <div v-if="leftMode !== 'list'" class="mb-4">
          <Breadcrumb
            :items="[
              { label: '订单列表', onClick: () => leftMode = 'list' },
              { label: '通知详情' }
            ]"
          />
        </div>

        <!-- 列表模式 -->
        <div v-if="leftMode === 'list'" class="space-y-4">
          <!-- 统计信息 -->
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-3">
              <h2 class="text-lg font-semibold text-foreground">
                {{ currentModule === 'order' ? '订单列表' : '报价单列表' }}
              </h2>
              <button
                class="inline-flex items-center gap-1.5 px-3 py-1.5 text-xs font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
                :class="isSelectionMode ? 'bg-primary text-primary-foreground' : ''"
                @click="toggleSelectionMode"
              >
                <List class="h-3.5 w-3.5" />
                {{ isSelectionMode ? '退出选择' : '选择模式' }}
              </button>
            </div>
            <div class="flex items-center gap-2 text-sm text-muted-foreground">
              <Filter class="h-4 w-4" />
              共 {{ filteredOrders.length }} 条记录
            </div>
          </div>

          <!-- 订单卡片列表 -->
          <SelectionMode
            v-if="currentModule === 'order'"
            :items="filteredOrders"
            :selectable="isSelectionMode"
            @selection-change="handleSelectionChange"
            @batch-delete="handleBatchDelete"
            @batch-invoice="handleBatchInvoice"
            @batch-export="handleBatchExport"
          >
            <template #default="{ isSelected, toggleSelect }">
              <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4">
                <div
                  v-for="order in filteredOrders"
                  :key="order.id"
                  class="relative"
                >
                  <!-- 选择框 -->
                  <div
                    v-if="isSelectionMode"
                    class="absolute top-2 left-2 z-10"
                    @click.stop
                  >
                    <button
                      class="inline-flex items-center justify-center rounded-md h-6 w-6 bg-background border border-border hover:bg-accent transition-colors"
                      :class="isSelected(order.id) ? 'text-primary' : 'text-muted-foreground'"
                      @click="toggleSelect(order)"
                    >
                      <CheckSquare v-if="isSelected(order.id)" class="h-4 w-4" />
                      <Square v-else class="h-4 w-4" />
                    </button>
                  </div>

                  <!-- 订单卡片（内联实现） -->
                  <div
                    class="rounded-lg border border-border bg-card cursor-pointer hover:shadow-md transition-shadow"
                    @click="handleOrderClick(order, false)"
                  >
                    <div class="px-4 py-3 border-b border-border bg-muted/30">
                      <div class="flex items-center justify-between gap-2">
                        <div class="flex items-center gap-2 flex-1 min-w-0">
                          <span class="text-sm font-medium text-foreground truncate">订单 {{ order.number }}</span>
                          <button
                            class="p-1 rounded hover:bg-accent transition-colors"
                            title="点击复制编号"
                            @click.stop="handleCopyNumber(order.number)"
                          >
                            <Copy class="h-3.5 w-3.5 text-muted-foreground" />
                          </button>
                        </div>
                        <StatusBadge :status="order.status" />
                      </div>
                    </div>
                    <div class="px-4 py-3 space-y-2">
                      <div class="flex items-center justify-between text-sm">
                        <span class="text-muted-foreground">{{ order.customerName }}</span>
                        <span class="text-muted-foreground">{{ order.createdAt }}</span>
                      </div>
                      <div v-if="order.progress" class="flex items-center justify-between text-sm">
                        <span class="text-muted-foreground">入库进度</span>
                        <span class="font-medium text-foreground">{{ order.progress }}</span>
                      </div>
                      <div class="flex items-center justify-between text-sm">
                        <span class="text-muted-foreground">项目数</span>
                        <span class="font-medium text-foreground">{{ order.itemCount }} 项</span>
                      </div>
                      <div class="flex items-center justify-between text-sm pt-1 border-t border-border">
                        <span class="text-muted-foreground">总金额</span>
                        <span class="font-semibold text-foreground">¥{{ order.totalAmount.toLocaleString('zh-CN', { minimumFractionDigits: 2 }) }}</span>
                      </div>
                    </div>
                    <div class="px-4 py-3 border-t border-border bg-muted/20 flex flex-wrap gap-2">
                      <button class="px-3 py-1.5 text-xs font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors">
                        查看
                      </button>
                      <button
                        class="px-3 py-1.5 text-xs font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
                        @click.stop="handleOrderClick(order, true)"
                      >
                        编辑
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </template>
          </SelectionMode>

          <!-- 阴阳项目卡片列表（演示用） -->
          <div v-if="currentModule === 'quotation'" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4">
            <DualItemCard
              v-for="dualItem in mockDualItems"
              :key="dualItem.productionItem.id"
              :item="dualItem.productionItem"
              :dual-items="dualItem.customerItems"
              :is-duality="dualItem.customerItems.length > 0"
              @click="handleDualItemClick"
              @view-dual="handleViewDual"
            />
          </div>

          <!-- 空状态 -->
          <div v-if="filteredOrders.length === 0 && currentModule === 'order'" class="text-center py-12">
            <p class="text-muted-foreground">暂无数据</p>
          </div>
        </div>

        <!-- 通知详情模式 -->
        <div v-else-if="leftMode === 'notification-detail'" class="space-y-4">
          <h2 class="text-lg font-semibold text-foreground">待开票订单项</h2>
          <p class="text-muted-foreground">从通知进入的详情页面</p>
          <!-- TODO: 实现通知详情内容 -->
        </div>
      </main>

      <!-- 右侧悬浮框 -->
      <aside class="sticky top-14 h-[calc(100vh-3.5rem-3rem)]">
        <RightPanel
          :current-module="currentModule"
          :unread-notification-count="unreadNotificationCount"
          @module-change="handleModuleChange"
          @tab-change="handleTabChange"
          @search="handleSearch"
          @create-new="handleCreateNew"
          @view-all-notifications="handleViewAllNotifications"
          @notification-click="handleNotificationClick"
        />
      </aside>
    </div>

    <!-- 订单详情抽屉 -->
    <Drawer
      v-model:open="showDrawer"
      :title="selectedOrder ? `订单 ${selectedOrder.number}` : '订单详情'"
    >
      <template #list>
        <div class="space-y-3">
          <div class="flex items-center justify-between mb-3">
            <h3 class="text-sm font-semibold text-foreground">订单项列表</h3>
            <button
              v-if="!isEditing"
              class="px-3 py-1.5 text-xs font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
              @click="handleEditOrder"
            >
              编辑
            </button>
            <span
              v-else
              class="inline-flex items-center gap-1.5 px-2 py-1 text-xs font-medium rounded-md bg-amber-100 text-amber-800 dark:bg-amber-900/30 dark:text-amber-300"
            >
              <span class="h-1.5 w-1.5 rounded-full bg-amber-500"></span>
              编辑模式
            </span>
          </div>
          <div v-for="dualItem in mockDualItems" :key="dualItem.productionItem.id">
            <DualItemCard
              :item="dualItem.productionItem"
              :dual-items="dualItem.customerItems"
              :is-duality="dualItem.customerItems.length > 0"
              @click="handleDualItemClick"
              @view-dual="handleViewDual"
            />
          </div>
        </div>
      </template>

      <template #detail>
        <div v-if="selectedOrder" class="space-y-4">
          <h3 class="text-sm font-semibold text-foreground">订单信息</h3>
          <div class="space-y-2">
            <div class="flex items-center justify-between text-sm">
              <span class="text-muted-foreground">订单编号：</span>
              <span class="font-medium text-foreground">{{ selectedOrder.number }}</span>
            </div>
            <div class="flex items-center justify-between text-sm">
              <span class="text-muted-foreground">客户名称：</span>
              <span class="font-medium text-foreground">{{ selectedOrder.customerName }}</span>
            </div>
            <div class="flex items-center justify-between text-sm">
              <span class="text-muted-foreground">订单状态：</span>
              <StatusBadge :status="selectedOrder.status" />
            </div>
            <div class="flex items-center justify-between text-sm">
              <span class="text-muted-foreground">创建日期：</span>
              <span class="font-medium text-foreground">{{ selectedOrder.createdAt }}</span>
            </div>
            <div class="flex items-center justify-between text-sm">
              <span class="text-muted-foreground">项目数量：</span>
              <span class="font-medium text-foreground">{{ selectedOrder.itemCount }}</span>
            </div>
            <div class="flex items-center justify-between text-sm">
              <span class="text-muted-foreground">订单金额：</span>
              <span class="font-semibold text-foreground">¥{{ selectedOrder.totalAmount.toFixed(2) }}</span>
            </div>
          </div>

          <div class="pt-4 border-t border-border">
            <h3 class="text-sm font-semibold text-foreground mb-2">操作记录</h3>
            <div class="space-y-2 text-sm text-muted-foreground">
              <p>• 2026-06-18 10:30 创建订单</p>
              <p>• 2026-06-18 14:20 开始生产</p>
              <p>• 2026-06-19 09:00 进度更新</p>
            </div>
          </div>
        </div>
      </template>

      <template #actions>
        <button
          v-if="isEditing"
          class="px-4 py-2 text-sm font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
          @click="isEditing = false"
        >
          取消
        </button>
        <button
          class="px-4 py-2 text-sm font-medium rounded-md border border-input bg-background hover:bg-accent transition-colors"
          @click="showApprovalFlow = true"
        >
          审批流程
        </button>
        <button
          class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
          @click="isEditing = false; showDrawer = false"
        >
          确定
        </button>
      </template>
    </Drawer>

    <!-- 联合开票流程 -->
    <InvoiceProcess
      v-model:open="showInvoiceProcess"
      :items="mockInvoiceItems"
      @confirm="handleInvoiceConfirm"
    />

    <!-- 审批流程 -->
    <ApprovalFlow
      v-model:open="showApprovalFlow"
      :flow="mockApprovalFlow"
      @approve="handleApprovalApprove"
      @reject="handleApprovalReject"
    />
  </div>
</template>
