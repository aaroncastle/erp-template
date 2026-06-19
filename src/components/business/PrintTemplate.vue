<script setup lang="ts">
import { Printer, Download, X } from '@lucide/vue'
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'

export type PrintType = 'order' | 'invoice' | 'quotation'

export interface PrintData {
  type: PrintType
  title: string
  number: string
  date: string
  customerName: string
  items: PrintItem[]
  totalAmount: number
  remark?: string
}

export interface PrintItem {
  name: string
  specification?: string
  quantity: number
  unitPrice: number
  amount: number
}

interface Props {
  open?: boolean
  data?: PrintData
}

const props = withDefaults(defineProps<Props>(), {
  open: false,
})

const emit = defineEmits<{
  (e: 'update:open', value: boolean): void
  (e: 'print'): void
  (e: 'download'): void
  (e: 'close'): void
}>()

function handleClose() {
  emit('update:open', false)
  emit('close')
}

function handlePrint() {
  window.print()
  emit('print')
}

function handleDownload() {
  emit('download')
}

function formatAmount(amount: number): string {
  return amount.toFixed(2)
}
</script>

<template>
  <Teleport to="body">
    <div
      v-if="open && data"
      class="fixed inset-0 z-[200] bg-black/50 backdrop-blur-sm flex items-center justify-center"
      @click.self="handleClose"
    >
      <Card class="w-[800px] max-h-[90vh] flex flex-col bg-background">
        <CardHeader class="flex-shrink-0">
          <div class="flex items-center justify-between">
            <CardTitle>{{ data.title }}</CardTitle>
            <div class="flex items-center gap-2">
              <Button variant="outline" size="sm" @click="handlePrint">
                <Printer class="h-4 w-4 mr-1" />
                打印
              </Button>
              <Button variant="outline" size="sm" @click="handleDownload">
                <Download class="h-4 w-4 mr-1" />
                下载
              </Button>
              <Button variant="ghost" size="sm" @click="handleClose">
                <X class="h-4 w-4" />
              </Button>
            </div>
          </div>
        </CardHeader>

        <CardContent class="flex-1 overflow-y-auto">
          <!-- 打印内容区域 -->
          <div class="p-6 border border-border rounded-lg bg-background print-area">
            <!-- 标题 -->
            <div class="text-center mb-6 pb-4 border-b-2 border-foreground">
              <h1 class="text-2xl font-bold text-foreground">
                {{ data.type === 'order' ? '订单' : data.type === 'invoice' ? '发票' : '报价单' }}
              </h1>
              <p class="text-sm text-muted-foreground mt-2">
                编号：{{ data.number }}
              </p>
            </div>

            <!-- 基本信息 -->
            <div class="grid grid-cols-2 gap-4 mb-6">
              <div>
                <p class="text-sm font-medium text-foreground">客户名称</p>
                <p class="text-base text-foreground mt-1">{{ data.customerName }}</p>
              </div>
              <div class="text-right">
                <p class="text-sm font-medium text-foreground">日期</p>
                <p class="text-base text-foreground mt-1">{{ data.date }}</p>
              </div>
            </div>

            <!-- 项目列表 -->
            <div class="mb-6">
              <table class="w-full border-collapse">
                <thead>
                  <tr class="border-b-2 border-foreground">
                    <th class="text-left py-2 px-2 text-sm font-bold text-foreground">产品名称</th>
                    <th class="text-left py-2 px-2 text-sm font-bold text-foreground">规格</th>
                    <th class="text-right py-2 px-2 text-sm font-bold text-foreground">数量</th>
                    <th class="text-right py-2 px-2 text-sm font-bold text-foreground">单价</th>
                    <th class="text-right py-2 px-2 text-sm font-bold text-foreground">金额</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="(item, index) in data.items"
                    :key="index"
                    class="border-b border-foreground/20"
                  >
                    <td class="py-2 px-2 text-sm text-foreground">{{ item.name }}</td>
                    <td class="py-2 px-2 text-sm text-foreground">{{ item.specification || '-' }}</td>
                    <td class="py-2 px-2 text-sm text-foreground text-right">{{ item.quantity }}</td>
                    <td class="py-2 px-2 text-sm text-foreground text-right">¥{{ formatAmount(item.unitPrice) }}</td>
                    <td class="py-2 px-2 text-sm text-foreground text-right font-medium">¥{{ formatAmount(item.amount) }}</td>
                  </tr>
                </tbody>
              </table>
            </div>

            <!-- 总计 -->
            <div class="flex justify-end mb-6">
              <div class="w-64">
                <div class="flex items-center justify-between py-2 border-t-2 border-foreground">
                  <span class="text-base font-bold text-foreground">总计</span>
                  <span class="text-xl font-bold text-primary">¥{{ formatAmount(data.totalAmount) }}</span>
                </div>
              </div>
            </div>

            <!-- 备注 -->
            <div v-if="data.remark" class="pt-4 border-t border-foreground/20">
              <p class="text-sm font-medium text-foreground">备注</p>
              <p class="text-sm text-foreground mt-1">{{ data.remark }}</p>
            </div>

            <!-- 底部信息 -->
            <div class="mt-8 pt-4 border-t border-foreground/20 text-xs text-muted-foreground text-center">
              <p>本{{ data.type === 'order' ? '订单' : data.type === 'invoice' ? '发票' : '报价单' }}由系统自动生成</p>
              <p>打印时间：{{ new Date().toLocaleString('zh-CN') }}</p>
            </div>
          </div>
        </CardContent>
      </Card>
    </div>
  </Teleport>
</template>

<style>
@media print {
  body * {
    visibility: hidden;
  }
  .print-area,
  .print-area * {
    visibility: visible;
  }
  .print-area {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    border: none !important;
    padding: 20px !important;
  }
}
</style>
