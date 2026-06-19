<script setup lang="ts">
import { ref, computed } from 'vue'
import InvoiceProcess, { type InvoiceMode } from '@/components/business/InvoiceProcess.vue'
import ApprovalFlow from '@/components/business/ApprovalFlow.vue'
import OrderDetailDrawer, { type OrderDetail } from '@/components/business/OrderDetailDrawer.vue'
import NotificationDrawer, { type Notification } from '@/components/business/NotificationDrawer.vue'
import QuotationCard, { type QuotationStatus } from '@/components/business/QuotationCard.vue'
import OrderEditForm, { type OrderData } from '@/components/business/OrderEditForm.vue'
import CustomerSelect, { type Customer } from '@/components/business/CustomerSelect.vue'
import DashboardCharts, { type ChartData, type StatCard } from '@/components/business/DashboardCharts.vue'
import FileUpload, { type UploadFile } from '@/components/business/FileUpload.vue'
import PrintTemplate, { type PrintData } from '@/components/business/PrintTemplate.vue'
import StatisticsCard, { type StatData } from '@/components/business/StatisticsCard.vue'
import DataTable, { type Column } from '@/components/business/DataTable.vue'
import Modal from '@/components/business/Modal.vue'
import Timeline, { type TimelineItem } from '@/components/business/Timeline.vue'
import CustomerDetail, { type CustomerDetail as CustomerDetailType } from '@/components/business/CustomerDetail.vue'
import ProductList, { type Product } from '@/components/business/ProductList.vue'
import ProgressBar from '@/components/business/ProgressBar.vue'
import EmptyState from '@/components/business/EmptyState.vue'
import Loading from '@/components/business/Loading.vue'

const invoiceMode = ref<InvoiceMode>('sales')
const showOrderDetail = ref(false)
const showNotification = ref(false)
const showCustomerSelect = ref(false)
const showPrintTemplate = ref(false)
const showModal = ref(false)
const showCustomerDetail = ref(false)
const showLoading = ref(false)

// 模拟订单详情数据
const mockOrderDetail = ref<OrderDetail>({
  id: '1',
  number: 'ORD-20260615-003',
  customerName: '洛阳轴承集团',
  status: 'in_progress',
  createdAt: '2026-06-15',
  updatedAt: '2026-06-19',
  itemCount: 3,
  totalAmount: 28500.00,
  progress: '3/5 项',
  deliveryStatus: 'pending_delivery',
  invoiceStatus: 'pending_invoice',
  items: [
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
  ],
  approvalFlow: {
    id: 'MOD-20260619-001',
    title: '订单 ORD-20260615-003 修改申请',
    status: 'pending',
    submitter: '张三（销售一部）',
    submitComment: '客户产品规格变更，需要调整订单内容',
    businessData: {
      type: 'order',
      orderNumber: 'ORD-20260615-003',
      customerName: '洛阳轴承集团',
      totalAmount: 28500.00,
      modifications: [
        {
          field: '产品规格',
          before: 'Φ100×Φ120×50',
          after: 'Φ100×Φ120×55',
        },
        {
          field: '数量',
          before: '10',
          after: '15',
        },
      ],
    },
    records: [
      {
        id: '1',
        approver: '张三',
        department: '销售一部',
        status: 'approved',
        comment: '同意修改',
        timestamp: '2026-06-18 10:30',
      },
      {
        id: '2',
        approver: '李四',
        department: '综合办',
        status: 'pending',
        timestamp: '',
      },
    ],
    currentStep: 2,
    totalSteps: 2,
  },
})

// 模拟通知数据
const mockNotifications = ref<Notification[]>([
  {
    id: '1',
    type: 'pending_invoice',
    title: '待开票通知',
    description: '订单 ORD-20260615-003',
    timestamp: '10 分钟前',
    status: 'unread',
    businessId: 'ORD-20260615-003',
    businessType: 'order',
  },
  {
    id: '2',
    type: 'modification_request',
    title: '修改申请待审批',
    description: '订单 ORD-20260610-002 规格变更',
    timestamp: '1 小时前',
    status: 'unread',
    businessId: 'MOD-20260619-001',
    businessType: 'approval',
  },
  {
    id: '3',
    type: 'storage_complete',
    title: '入库完成',
    description: '订单 ORD-20260608-001',
    timestamp: '2 小时前',
    status: 'unread',
    businessId: 'ORD-20260608-001',
    businessType: 'order',
  },
  {
    id: '4',
    type: 'approval_pending',
    title: '审批待处理',
    description: '采购申请 PR-20260619-001',
    timestamp: '3 小时前',
    status: 'read',
    businessId: 'PR-20260619-001',
    businessType: 'approval',
  },
  {
    id: '5',
    type: 'order_update',
    title: '订单状态更新',
    description: '订单 ORD-20260605-001 已发货',
    timestamp: '昨天',
    status: 'read',
    businessId: 'ORD-20260605-001',
    businessType: 'order',
  },
])

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
    customContent: '', // 销售自定义的开票内容
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
    customContent: '开具增值税专用发票，税率 13%', // 销售自定义内容示例
  },
  {
    id: '3',
    orderNumber: 'ORD-20260618-001',
    customerName: '洛阳能源密封件',
    productName: '机械密封 C 型',
    specification: 'Φ80×Φ100×40',
    quantity: 15,
    unitPrice: 650.00,
    amount: 9750.00,
    customContent: '',
  },
])

// 模拟订单修改申请审批数据（综合办处理）
const mockOrderModificationFlow = ref({
  id: 'MOD-20260619-001',
  title: '订单 ORD-20260615-003 修改申请',
  status: 'pending' as const,
  submitter: '张三（销售一部）',
  submitComment: '客户产品规格变更，需要调整订单内容',
  businessData: {
    type: 'order' as const,
    orderNumber: 'ORD-20260615-003',
    customerName: '洛阳轴承集团',
    totalAmount: 28500.00,
    modifications: [
      {
        field: '产品规格',
        before: 'Φ100×Φ120×50',
        after: 'Φ100×Φ120×55',
      },
      {
        field: '数量',
        before: '10',
        after: '15',
      },
    ],
  },
  records: [
    {
      id: '1',
      approver: '张三',
      department: '销售一部',
      status: 'approved' as const,
      comment: '同意修改',
      timestamp: '2026-06-18 10:30',
    },
    {
      id: '2',
      approver: '李四',
      department: '综合办',
      status: 'pending' as const,
      timestamp: '',
    },
  ],
  currentStep: 2,
  totalSteps: 2,
})

// 模拟客户信息修改审批数据（销售部主管处理）
const mockCustomerInfoFlow = ref({
  id: 'CUST-20260619-001',
  title: '客户信息修改申请',
  status: 'pending' as const,
  submitter: '张三（销售一部）',
  submitComment: '客户联系电话和地址变更',
  businessData: {
    type: 'customer_info' as const,
    customerName: '洛阳轴承集团',
    modifications: [
      {
        field: '联系电话',
        before: '138****1234',
        after: '139****5678',
      },
      {
        field: '联系地址',
        before: '洛阳市老城区 A 路 1 号',
        after: '洛阳市涧西区 B 路 2 号',
      },
    ],
  },
  records: [
    {
      id: '1',
      approver: '孙总',
      department: '销售部',
      status: 'pending' as const,
      timestamp: '',
    },
  ],
  currentStep: 1,
  totalSteps: 1,
  ccList: [
    { name: '李四', department: '综合办' },
  ],
})

const approvalFlowType = ref<'modification' | 'customer_info'>('modification')
const currentApprovalFlow = computed(() => {
  return approvalFlowType.value === 'modification' ? mockOrderModificationFlow.value : mockCustomerInfoFlow.value
})

const showInvoice = ref(false)
const showApproval = ref(false)

function handleInvoiceConfirm(items: any[]) {
  console.log('销售申请开票:', items)
  showInvoice.value = false
}

function handleInvoiceUpload(file: File, items: any[]) {
  console.log('财务上传发票:', file, items)
  showInvoice.value = false
}

function handleApprovalApprove(comment: string, ccList: any[]) {
  console.log('审批通过:', comment, '抄送人:', ccList)
  showApproval.value = false
}

function handleApprovalReject(comment: string, ccList: any[]) {
  console.log('审批驳回:', comment, '抄送人:', ccList)
  showApproval.value = false
}

function handleOrderEdit(order: OrderDetail) {
  console.log('编辑订单:', order)
  showOrderDetail.value = false
}

function handleOrderCreateInvoice(order: OrderDetail) {
  console.log('申请开票:', order)
  showOrderDetail.value = false
}

function handleOrderShip(order: OrderDetail) {
  console.log('发货:', order)
  showOrderDetail.value = false
}

function handleOrderComplete(order: OrderDetail) {
  console.log('归档订单:', order)
  showOrderDetail.value = false
}

function handleNotificationClick(notification: Notification) {
  console.log('点击通知:', notification)
  // 根据通知类型跳转到对应业务
  if (notification.businessType === 'order') {
    console.log('跳转到订单详情:', notification.businessId)
  } else if (notification.businessType === 'approval') {
    console.log('跳转到审批详情:', notification.businessId)
  }
}

function handleMarkAsRead(notificationId: string) {
  console.log('标记已读:', notificationId)
  const notification = mockNotifications.value.find(n => n.id === notificationId)
  if (notification) {
    notification.status = 'read'
  }
}

function handleMarkAllAsRead() {
  console.log('全部标记已读')
  mockNotifications.value.forEach(n => {
    n.status = 'read'
  })
}

// 模拟报价单数据
const mockQuotations = ref([
  {
    quotationNumber: 'QUO-20260619-001',
    customerName: '洛阳轴承集团',
    totalAmount: 15000.00,
    itemCount: 2,
    status: 'pending' as QuotationStatus,
    validUntil: '2026-07-19',
    createdAt: '2026-06-19',
  },
  {
    quotationNumber: 'QUO-20260618-002',
    customerName: '郑州机械公司',
    totalAmount: 28000.00,
    itemCount: 3,
    status: 'confirmed' as QuotationStatus,
    validUntil: '2026-07-18',
    createdAt: '2026-06-18',
  },
])

// 模拟客户数据
const mockCustomers = ref<Customer[]>([
  {
    id: '1',
    name: '洛阳轴承集团',
    contact: '张经理',
    phone: '138****1234',
    address: '洛阳市老城区 A 路 1 号',
    recent: true,
  },
  {
    id: '2',
    name: '郑州机械公司',
    contact: '李总',
    phone: '139****5678',
    address: '郑州市高新区 B 路 2 号',
    recent: true,
  },
  {
    id: '3',
    name: '开封液压设备',
    contact: '王工',
    phone: '137****9012',
    address: '开封市工业园区 C 路 3 号',
  },
])

const mockRecentCustomers = ref<Customer[]>([
  {
    id: '1',
    name: '洛阳轴承集团',
    contact: '张经理',
    phone: '138****1234',
    recent: true,
  },
  {
    id: '2',
    name: '郑州机械公司',
    contact: '李总',
    phone: '139****5678',
    recent: true,
  },
])

function handleQuotationClick(quotationNumber: string) {
  console.log('点击报价单:', quotationNumber)
}

function handleQuotationEdit(quotationNumber: string) {
  console.log('编辑报价单:', quotationNumber)
}

function handleQuotationDelete(quotationNumber: string) {
  console.log('删除报价单:', quotationNumber)
}

function handleCustomerSelect(customerId: string, customerName: string) {
  console.log('选择客户:', customerId, customerName)
  showCustomerSelect.value = false
}

// 模拟统计数据
const mockStats = ref<StatCard[]>([
  { title: '本月订单', value: 156, change: 12, icon: 'orders' },
  { title: '本月金额', value: '¥285,600', change: 8, icon: 'dollar' },
  { title: '客户数量', value: 42, change: -2, icon: 'users' },
  { title: '待处理', value: 18, change: 0, icon: 'package' },
])

const mockOrderStatusChart = ref<ChartData[]>([
  { label: '草稿', value: 15, color: '#6b7280' },
  { label: '待处理', value: 23, color: '#eab308' },
  { label: '进行中', value: 45, color: '#3b82f6' },
  { label: '已完成', value: 73, color: '#22c55e' },
])

const mockAmountTrendChart = ref<ChartData[]>([
  { label: '1月', value: 125000 },
  { label: '2月', value: 148000 },
  { label: '3月', value: 167000 },
  { label: '4月', value: 189000 },
  { label: '5月', value: 215000 },
  { label: '6月', value: 285600 },
])

const mockDepartmentPerformance = ref<ChartData[]>([
  { label: '销售一部', value: 125000, color: '#3b82f6' },
  { label: '销售二部', value: 98000, color: '#22c55e' },
  { label: '销售三部', value: 87000, color: '#eab308' },
  { label: '销售四部', value: 65000, color: '#ef4444' },
])

// 模拟统计卡片数据
const mockStatCards = ref<StatData[]>([
  { title: '订单总数', value: 156, change: 12, icon: 'orders', color: 'blue' },
  { title: '总金额', value: '¥285,600', change: 8, icon: 'dollar', color: 'green' },
  { title: '客户数', value: 42, change: -2, icon: 'users', color: 'purple' },
  { title: '报价单', value: 28, change: 5, icon: 'quotation', color: 'orange' },
])

// 模拟打印数据
const mockPrintData = ref<PrintData>({
  type: 'order',
  title: '销售订单',
  number: 'ORD-20260619-001',
  date: '2026-06-19',
  customerName: '洛阳轴承集团',
  items: [
    {
      name: '机械密封 A 型',
      specification: 'Φ100×Φ120×50',
      quantity: 10,
      unitPrice: 850.00,
      amount: 8500.00,
    },
    {
      name: '机械密封 B 型',
      specification: 'Φ120×Φ140×60',
      quantity: 8,
      unitPrice: 950.00,
      amount: 7600.00,
    },
    {
      name: '机械密封 C 型',
      specification: 'Φ80×Φ100×40',
      quantity: 15,
      unitPrice: 650.00,
      amount: 9750.00,
    },
  ],
  totalAmount: 25850.00,
  remark: '请尽快安排生产，客户急需。',
})

function handleUpload(file: UploadFile) {
  console.log('上传文件:', file.name)
}

function handleRemove(fileId: string) {
  console.log('移除文件:', fileId)
}

// 模拟表格数据
const mockTableColumns = ref<Column<any>[]>([
  { key: 'id', label: 'ID', width: '80px', sortable: true },
  { key: 'name', label: '名称', sortable: true },
  { key: 'category', label: '分类', sortable: true },
  { key: 'price', label: '价格', width: '120px', sortable: true, align: 'right' },
  { key: 'stock', label: '库存', width: '100px', sortable: true, align: 'right' },
])

const mockTableData = ref([
  { id: 1, name: '机械密封 A 型', category: '密封件', price: 850.00, stock: 100 },
  { id: 2, name: '机械密封 B 型', category: '密封件', price: 950.00, stock: 80 },
  { id: 3, name: '机械密封 C 型', category: '密封件', price: 650.00, stock: 150 },
  { id: 4, name: '液压泵 A', category: '液压设备', price: 2500.00, stock: 30 },
  { id: 5, name: '液压泵 B', category: '液压设备', price: 3200.00, stock: 25 },
])

// 模拟时间线数据
const mockTimelineItems = ref<TimelineItem[]>([
  {
    id: '1',
    title: '订单创建',
    description: '销售一部创建订单',
    timestamp: '2026-06-15 10:30',
    status: 'completed',
  },
  {
    id: '2',
    title: '审批通过',
    description: '综合办审批通过',
    timestamp: '2026-06-15 14:20',
    status: 'completed',
  },
  {
    id: '3',
    title: '生产中',
    description: '生产车间开始生产',
    timestamp: '2026-06-16 09:00',
    status: 'in_progress',
  },
  {
    id: '4',
    title: '待发货',
    description: '生产完成，等待发货',
    timestamp: '',
    status: 'pending',
  },
])

// 模拟客户详情数据
const mockCustomerDetail = ref<CustomerDetailType>({
  id: '1',
  name: '洛阳轴承集团',
  contact: '张经理',
  phone: '138****1234',
  email: 'zhang@example.com',
  address: '洛阳市老城区 A 路 1 号',
  company: '洛阳轴承集团有限公司',
  status: 'active',
  createdAt: '2026-01-15',
  orderCount: 28,
  totalAmount: 568000.00,
  remark: '重要客户，优先处理',
})

// 模拟产品列表数据
const mockProducts = ref<Product[]>([
  {
    id: '1',
    name: '机械密封 A 型',
    specification: 'Φ100×Φ120×50',
    category: '密封件',
    unit: '个',
    price: 850.00,
    cost: 500.00,
    stock: 100,
    status: 'active',
  },
  {
    id: '2',
    name: '机械密封 B 型',
    specification: 'Φ120×Φ140×60',
    category: '密封件',
    unit: '个',
    price: 950.00,
    cost: 550.00,
    stock: 80,
    status: 'active',
  },
  {
    id: '3',
    name: '机械密封 C 型',
    specification: 'Φ80×Φ100×40',
    category: '密封件',
    unit: '个',
    price: 650.00,
    cost: 380.00,
    stock: 150,
    status: 'inactive',
  },
])
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">P2 业务组件演示页面</p>
      </div>

      <!-- 联合开票流程 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">联合开票流程</h2>
            <p class="text-sm text-muted-foreground mt-1">
              支持销售申请和财务上传两种模式
            </p>
          </div>
          <div class="flex items-center gap-2">
            <!-- 模式切换 -->
            <div class="inline-flex rounded-md border border-border p-1">
              <button
                class="px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
                :class="invoiceMode === 'sales' ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:text-foreground'"
                @click="invoiceMode = 'sales'"
              >
                销售申请
              </button>
              <button
                class="px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
                :class="invoiceMode === 'finance' ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:text-foreground'"
                @click="invoiceMode = 'finance'"
              >
                财务上传
              </button>
            </div>
            <button
              class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
              @click="showInvoice = true"
            >
              打开开票流程
            </button>
          </div>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">销售申请模式（3 步）：</p>
          <p>• 选择开票项目</p>
          <p>• 填写自定义开票内容（可选）</p>
          <p>• 确认申请</p>
          <p class="font-medium text-foreground mt-3">财务上传模式（4 步）：</p>
          <p>• 查看销售申请的项目（已选中，不可取消）</p>
          <p>• 预览开票内容（可复制）</p>
          <p>• 上传实际发票（支持拖拽）</p>
          <p>• 完成并生成预览链接</p>
        </div>
      </div>

      <!-- 审批流程 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">审批流程</h2>
            <p class="text-sm text-muted-foreground mt-1">
              多级审批流程，支持通过/驳回操作
            </p>
          </div>
          <div class="flex items-center gap-2">
            <!-- 类型切换 -->
            <div class="inline-flex rounded-md border border-border p-1">
              <button
                class="px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
                :class="approvalFlowType === 'modification' ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:text-foreground'"
                @click="approvalFlowType = 'modification'"
              >
                订单修改
              </button>
              <button
                class="px-3 py-1.5 text-xs font-medium rounded-md transition-colors"
                :class="approvalFlowType === 'customer_info' ? 'bg-primary text-primary-foreground' : 'text-muted-foreground hover:text-foreground'"
                @click="approvalFlowType = 'customer_info'"
              >
                客户信息修改
              </button>
            </div>
            <button
              class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
              @click="showApproval = true"
            >
              打开审批流程
            </button>
          </div>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 显示提交人及审批原因</p>
          <p>• Hover 查看业务数据（订单信息、修改内容对比）</p>
          <p>• 综合办只能看到客户名称（不显示完整客户信息）</p>
          <p>• 客户信息修改需销售部主管审批</p>
          <p>• 审批进度条显示</p>
          <p>• 通过/驳回操作（驳回原因必填）</p>
          <p>• 抄送人功能（主动添加，可选）</p>
        </div>
      </div>

      <!-- 订单详情抽屉 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">订单详情抽屉</h2>
            <p class="text-sm text-muted-foreground mt-1">
              左右分栏布局，集成阴阳项目和审批流程
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showOrderDetail = true"
          >
            打开订单详情
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 左侧：订单项列表（阴阳项目卡片）</p>
          <p>• 右侧：订单信息、进度、审批流程入口</p>
          <p>• 底部：根据状态显示操作按钮（编辑/开票/发货/归档）</p>
          <p>• 集成 ApprovalFlow 组件查看审批详情</p>
        </div>
      </div>

      <!-- 通知中心抽屉 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">通知中心抽屉</h2>
            <p class="text-sm text-muted-foreground mt-1">
              未读/已读分类，点击跳转到对应业务
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showNotification = true"
          >
            打开通知中心
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 未读/全部 Tab 切换</p>
          <p>• 通知类型图标（待开票/修改申请/入库完成/待审批/订单更新）</p>
          <p>• 未读标记（蓝色圆点）</p>
          <p>• 全部已读功能</p>
          <p>• 点击通知跳转到对应业务详情</p>
        </div>
      </div>

      <!-- P1 新组件预览 -->

      <!-- 报价单卡片 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">报价单卡片</h2>
          <p class="text-sm text-muted-foreground mt-1">
            显示报价单信息，支持编辑/删除操作
          </p>
        </div>

        <div class="space-y-3">
          <QuotationCard
            v-for="quotation in mockQuotations"
            :key="quotation.quotationNumber"
            :quotation-number="quotation.quotationNumber"
            :customer-name="quotation.customerName"
            :total-amount="quotation.totalAmount"
            :item-count="quotation.itemCount"
            :status="quotation.status"
            :valid-until="quotation.validUntil"
            :created-at="quotation.createdAt"
            @click="handleQuotationClick(quotation.quotationNumber)"
            @edit="handleQuotationEdit(quotation.quotationNumber)"
            @delete="handleQuotationDelete(quotation.quotationNumber)"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 显示报价单编号、客户名称、金额、项目数</p>
          <p>• 状态徽章（待确认/已确认/已过期）</p>
          <p>• 有效期显示</p>
          <p>• 编辑/删除操作按钮</p>
        </div>
      </div>

      <!-- 订单编辑表单 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">订单编辑表单</h2>
          <p class="text-sm text-muted-foreground mt-1">
            创建/编辑订单，支持阴阳项目关联
          </p>
        </div>

        <OrderEditForm
          mode="create"
          @save="(data) => console.log('保存订单:', data)"
          @cancel="() => console.log('取消编辑')"
        />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 客户选择（集成 CustomerSelect）</p>
          <p>• 添加/删除订单项</p>
          <p>• 阴阳项目关联（客户指定项）</p>
          <p>• 自动计算总金额</p>
          <p>• 表单验证</p>
        </div>
      </div>

      <!-- 客户选择器 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">客户选择器</h2>
            <p class="text-sm text-muted-foreground mt-1">
              搜索/筛选客户，显示最近使用
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showCustomerSelect = true"
          >
            打开客户选择器
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 搜索客户名称/联系人/电话</p>
          <p>• 最近使用客户快速选择</p>
          <p>• 显示客户详细信息（联系人、电话、地址）</p>
          <p>• 选中状态标记</p>
        </div>
      </div>

      <!-- P2 新组件预览 -->

      <!-- 仪表盘图表 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">仪表盘图表</h2>
          <p class="text-sm text-muted-foreground mt-1">
            数据统计和图表展示
          </p>
        </div>

        <DashboardCharts
          :stats="mockStats"
          :order-status-chart="mockOrderStatusChart"
          :amount-trend-chart="mockAmountTrendChart"
          :department-performance="mockDepartmentPerformance"
        />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 统计卡片（订单数、金额、客户数、待处理）</p>
          <p>• 订单状态分布图</p>
          <p>• 金额趋势图</p>
          <p>• 部门业绩对比图</p>
        </div>
      </div>

      <!-- 文件上传 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">文件上传</h2>
          <p class="text-sm text-muted-foreground mt-1">
            支持拖拽上传、多文件、进度显示
          </p>
        </div>

        <FileUpload
          accept="image/*,.pdf,.doc,.docx"
          :max-size="10"
          :max-files="5"
          @upload="handleUpload"
          @remove="handleRemove"
        />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 拖拽上传</p>
          <p>• 多文件支持</p>
          <p>• 文件大小限制</p>
          <p>• 上传进度显示</p>
          <p>• 文件类型图标</p>
        </div>
      </div>

      <!-- 打印模板 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">打印模板</h2>
            <p class="text-sm text-muted-foreground mt-1">
              订单/发票/报价单打印预览
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showPrintTemplate = true"
          >
            打开打印模板
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 打印预览</p>
          <p>• 支持订单/发票/报价单</p>
          <p>• 打印/下载功能</p>
          <p>• 响应式布局</p>
        </div>
      </div>

      <!-- 统计卡片 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">统计卡片</h2>
          <p class="text-sm text-muted-foreground mt-1">
            单个统计数据展示
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          <StatisticsCard
            v-for="(stat, index) in mockStatCards"
            :key="index"
            :stat="stat"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 数据展示</p>
          <p>• 趋势变化</p>
          <p>• 图标支持</p>
          <p>• 多种颜色</p>
        </div>
      </div>

      <!-- P3 新组件预览 -->

      <!-- 数据表格 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">数据表格</h2>
          <p class="text-sm text-muted-foreground mt-1">
            高级数据表格，支持排序、筛选、分页、选择
          </p>
        </div>

        <DataTable
          :columns="mockTableColumns"
          :data="mockTableData"
          :selectable="true"
          :page-size="5"
          search-placeholder="搜索产品..."
          @select="(rows) => console.log('选择行:', rows)"
          @row-click="(row) => console.log('点击行:', row)"
        />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 搜索过滤</p>
          <p>• 列排序</p>
          <p>• 分页</p>
          <p>• 行选择</p>
          <p>• 加载状态</p>
        </div>
      </div>

      <!-- 模态框 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">模态框</h2>
            <p class="text-sm text-muted-foreground mt-1">
              通用弹窗组件
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showModal = true"
          >
            打开模态框
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 多种宽度（sm/md/lg/xl/full）</p>
          <p>• 可关闭</p>
          <p>• 支持头部操作</p>
          <p>• 支持底部操作</p>
        </div>
      </div>

      <!-- 时间线 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">时间线</h2>
          <p class="text-sm text-muted-foreground mt-1">
            操作记录、订单流程展示
          </p>
        </div>

        <Timeline :items="mockTimelineItems" />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 垂直/水平布局</p>
          <p>• 多种状态</p>
          <p>• 自定义图标</p>
          <p>• 时间戳显示</p>
        </div>
      </div>

      <!-- 客户详情 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">客户详情</h2>
            <p class="text-sm text-muted-foreground mt-1">
              客户信息展示和编辑
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showCustomerDetail = true"
          >
            打开客户详情
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 基本信息展示</p>
          <p>• 状态信息</p>
          <p>• 业务统计</p>
          <p>• 编辑/删除操作</p>
        </div>
      </div>

      <!-- 产品列表 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">产品列表</h2>
          <p class="text-sm text-muted-foreground mt-1">
            产品展示和管理
          </p>
        </div>

        <ProductList
          :products="mockProducts"
          @add="() => console.log('添加产品')"
          @edit="(product) => console.log('编辑产品:', product)"
          @delete="(product) => console.log('删除产品:', product)"
          @select="(product) => console.log('选择产品:', product)"
        />

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 产品搜索</p>
          <p>• 状态显示</p>
          <p>• 编辑/删除操作</p>
          <p>• 空状态提示</p>
        </div>
      </div>

      <!-- 进度条 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">进度条</h2>
          <p class="text-sm text-muted-foreground mt-1">
            进度展示
          </p>
        </div>

        <div class="space-y-4">
          <div>
            <p class="text-sm text-muted-foreground mb-2">小尺寸 (25%)</p>
            <ProgressBar :value="25" size="sm" />
          </div>
          <div>
            <p class="text-sm text-muted-foreground mb-2">中尺寸 (50%)</p>
            <ProgressBar :value="50" size="md" :show-label="true" />
          </div>
          <div>
            <p class="text-sm text-muted-foreground mb-2">大尺寸 (75%)</p>
            <ProgressBar :value="75" size="lg" :show-label="true" color="success" />
          </div>
          <div>
            <p class="text-sm text-muted-foreground mb-2">警告色 (90%)</p>
            <ProgressBar :value="90" :show-label="true" color="warning" />
          </div>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 多种尺寸</p>
          <p>• 多种颜色</p>
          <p>• 标签显示</p>
        </div>
      </div>

      <!-- 空状态 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">空状态</h2>
          <p class="text-sm text-muted-foreground mt-1">
            空数据提示
          </p>
        </div>

        <div class="grid grid-cols-2 gap-4">
          <EmptyState type="default" />
          <EmptyState type="data" />
          <EmptyState type="search" />
          <EmptyState type="error" />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 多种类型</p>
          <p>• 自定义标题和描述</p>
          <p>• 操作按钮</p>
        </div>
      </div>

      <!-- 加载状态 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">加载状态</h2>
            <p class="text-sm text-muted-foreground mt-1">
              加载动画
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showLoading = !showLoading"
          >
            {{ showLoading ? '关闭' : '打开' }}加载状态
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 多种尺寸</p>
          <p>• 全屏/内联模式</p>
          <p>• 自定义文本</p>
        </div>
      </div>

      <!-- 组件 -->
      <InvoiceProcess
        v-model:open="showInvoice"
        :items="mockInvoiceItems"
        :mode="invoiceMode"
        @submit="handleInvoiceConfirm"
        @upload="handleInvoiceUpload"
      />

      <ApprovalFlow
        v-model:open="showApproval"
        :flow="currentApprovalFlow"
        @approve="handleApprovalApprove"
        @reject="handleApprovalReject"
      />

      <OrderDetailDrawer
        v-model:open="showOrderDetail"
        :order="mockOrderDetail"
        :editable="true"
        @edit="handleOrderEdit"
        @create-invoice="handleOrderCreateInvoice"
        @ship="handleOrderShip"
        @complete="handleOrderComplete"
      />

      <NotificationDrawer
        v-model:open="showNotification"
        :notifications="mockNotifications"
        @notification-click="handleNotificationClick"
        @mark-as-read="handleMarkAsRead"
        @mark-all-as-read="handleMarkAllAsRead"
      />

      <CustomerSelect
        v-model:open="showCustomerSelect"
        :customers="mockCustomers"
        :recent-customers="mockRecentCustomers"
        @select="handleCustomerSelect"
      />

      <PrintTemplate
        v-model:open="showPrintTemplate"
        :data="mockPrintData"
        @print="() => console.log('打印')"
        @download="() => console.log('下载')"
      />

      <Modal
        v-model:open="showModal"
        title="模态框标题"
        width="md"
      >
        <p class="text-sm text-muted-foreground">
          这是模态框的内容区域。可以在这里放置任何内容。
        </p>
        <template #footer>
          <div class="flex items-center justify-end gap-2">
            <Button variant="outline" @click="showModal = false">
              取消
            </Button>
            <Button @click="showModal = false">
              确定
            </Button>
          </div>
        </template>
      </Modal>

      <CustomerDetail
        v-model:open="showCustomerDetail"
        :customer="mockCustomerDetail"
        @edit="(customer) => console.log('编辑客户:', customer)"
        @delete="(customer) => console.log('删除客户:', customer)"
      />

      <Loading :loading="showLoading" fullscreen text="加载中..." />
    </div>
  </div>
</template>
