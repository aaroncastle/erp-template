<script setup lang="ts">
import { ref } from 'vue'
import QuotationDetail, { type QuotationDetail as QuotationDetailType } from '@/components/business/QuotationDetail.vue'
import InvoiceCard, { type Invoice } from '@/components/business/InvoiceCard.vue'
import PaymentCard, { type Payment } from '@/components/business/PaymentCard.vue'
import WarehouseCard, { type WarehouseRecord } from '@/components/business/WarehouseCard.vue'

const showQuotationDetail = ref(false)

// 模拟报价单详情数据
const mockQuotationDetail = ref<QuotationDetailType>({
  id: '1',
  number: 'QUO-20260619-001',
  customerName: '洛阳轴承集团',
  contact: '张经理',
  phone: '138****1234',
  status: 'pending',
  totalAmount: 25850.00,
  validUntil: '2026-07-19',
  createdAt: '2026-06-19',
  updatedAt: '2026-06-19',
  items: [
    {
      id: '1',
      productName: '机械密封 A 型',
      specification: 'Φ100×Φ120×50',
      quantity: 10,
      unitPrice: 850.00,
      amount: 8500.00,
    },
    {
      id: '2',
      productName: '机械密封 B 型',
      specification: 'Φ120×Φ140×60',
      quantity: 8,
      unitPrice: 950.00,
      amount: 7600.00,
    },
    {
      id: '3',
      productName: '机械密封 C 型',
      specification: 'Φ80×Φ100×40',
      quantity: 15,
      unitPrice: 650.00,
      amount: 9750.00,
    },
  ],
  remark: '请尽快确认，客户急需。',
  createdBy: '张三（销售一部）',
})

// 模拟发票数据
const mockInvoices = ref<Invoice[]>([
  {
    id: '1',
    invoiceNumber: 'INV-20260619-001',
    orderNumber: 'ORD-20260615-001',
    customerName: '洛阳轴承集团',
    amount: 25850.00,
    taxAmount: 3355.50,
    totalAmount: 29205.50,
    issueDate: '2026-06-19',
    status: 'issued',
    type: 'vat_special',
  },
])

// 模拟收款数据
const mockPayments = ref<Payment[]>([
  {
    id: '1',
    paymentNumber: 'PAY-20260619-001',
    orderNumber: 'ORD-20260615-001',
    customerName: '洛阳轴承集团',
    amount: 29205.50,
    paidAmount: 15000.00,
    paymentDate: '2026-06-19',
    dueDate: '2026-07-19',
    status: 'partial',
    method: 'bank_transfer',
  },
])

// 模拟仓储记录数据
const mockWarehouseRecords = ref<WarehouseRecord[]>([
  {
    id: '1',
    recordNumber: 'WH-20260619-001',
    orderNumber: 'ORD-20260615-001',
    type: 'inbound',
    status: 'completed',
    operator: '李四（仓储部）',
    operationDate: '2026-06-19',
    items: [
      {
        id: '1',
        productName: '机械密封 A 型',
        specification: 'Φ100×Φ120×50',
        quantity: 10,
        unit: '个',
        location: 'A-01-01',
      },
      {
        id: '2',
        productName: '机械密封 B 型',
        specification: 'Φ120×Φ140×60',
        quantity: 8,
        unit: '个',
        location: 'A-01-02',
      },
    ],
  },
])

function handleQuotationEdit(quotation: QuotationDetailType) {
  console.log('编辑报价单:', quotation)
}

function handleQuotationDelete(quotation: QuotationDetailType) {
  console.log('删除报价单:', quotation)
}

function handleQuotationSubmit(quotation: QuotationDetailType) {
  console.log('提交报价单:', quotation)
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-7xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 3 - 业务组件</p>
      </div>

      <!-- Phase 3 组件预览 -->

      <!-- 报价单详情 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold text-foreground">报价单详情</h2>
            <p class="text-sm text-muted-foreground mt-1">
              报价单详细信息展示和编辑
            </p>
          </div>
          <button
            class="px-4 py-2 text-sm font-medium rounded-md bg-primary text-primary-foreground hover:bg-primary/90 transition-colors"
            @click="showQuotationDetail = true"
          >
            打开报价单详情
          </button>
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 报价项目列表</p>
          <p>• 客户信息展示</p>
          <p>• 日期信息</p>
          <p>• 编辑/删除/提交操作</p>
        </div>
      </div>

      <!-- 发票卡片 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">发票卡片</h2>
          <p class="text-sm text-muted-foreground mt-1">
            发票信息展示
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <InvoiceCard
            v-for="invoice in mockInvoices"
            :key="invoice.id"
            :invoice="invoice"
            @click="() => console.log('点击发票:', invoice.id)"
            @download="() => console.log('下载发票:', invoice.id)"
            @print="() => console.log('打印发票:', invoice.id)"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 发票信息展示</p>
          <p>• 金额和税额显示</p>
          <p>• 发票类型标识</p>
          <p>• 下载/打印操作</p>
        </div>
      </div>

      <!-- 收款卡片 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">收款卡片</h2>
          <p class="text-sm text-muted-foreground mt-1">
            收款信息展示
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <PaymentCard
            v-for="payment in mockPayments"
            :key="payment.id"
            :payment="payment"
            @click="() => console.log('点击收款:', payment.id)"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 收款信息展示</p>
          <p>• 应收/已收/待收金额</p>
          <p>• 支付方式显示</p>
          <p>• 到期日期提醒</p>
        </div>
      </div>

      <!-- 仓储卡片 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">仓储卡片</h2>
          <p class="text-sm text-muted-foreground mt-1">
            入库/出库记录展示
          </p>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <WarehouseCard
            v-for="record in mockWarehouseRecords"
            :key="record.id"
            :record="record"
            @click="() => console.log('点击仓储记录:', record.id)"
          />
        </div>

        <div class="text-sm text-muted-foreground space-y-1">
          <p class="font-medium text-foreground">功能特性：</p>
          <p>• 入库/出库标识</p>
          <p>• 物品列表展示</p>
          <p>• 库位信息</p>
          <p>• 操作人和日期</p>
        </div>
      </div>

      <!-- 组件实例 -->
      <QuotationDetail
        v-model:open="showQuotationDetail"
        :quotation="mockQuotationDetail"
        @edit="handleQuotationEdit"
        @delete="handleQuotationDelete"
        @submit="handleQuotationSubmit"
      />
    </div>
  </div>
</template>
