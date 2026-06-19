<script setup lang="ts">
import { ref } from 'vue'
import CardHeader from '@/components/business/CardHeader.vue'
import CopyableNumber from '@/components/business/CopyableNumber.vue'
import FilterTabs from '@/components/business/FilterTabs.vue'
import SearchFilterBar from '@/components/business/SearchFilterBar.vue'
import ConfirmDialog from '@/components/business/ConfirmDialog.vue'
import type { MenuItem } from '@/components/business/DropdownMenu.vue'

// CardHeader 菜单项
const headerMenuItems: MenuItem[] = [
  { key: 'edit', label: '编辑' },
  { key: 'duplicate', label: '复制' },
  { key: 'divider', label: '', divider: true },
  { key: 'delete', label: '删除', danger: true },
]

// FilterTabs 数据
const tabs = ref([
  { key: 'all', label: '全部', count: 12 },
  { key: 'pending', label: '待处理', count: 3 },
  { key: 'progress', label: '进行中', count: 5 },
  { key: 'completed', label: '已完成', count: 4 },
])
const activeTab = ref('all')

// SearchFilterBar 数据
const filters = [
  {
    key: 'customer',
    label: '客户',
    options: [
      { label: '洛阳轴承集团', value: '1' },
      { label: '中石化洛阳分公司', value: '2' },
      { label: '洛阳能源密封件', value: '3' },
    ],
  },
  {
    key: 'status',
    label: '状态',
    options: [
      { label: '待处理', value: 'pending' },
      { label: '进行中', value: 'progress' },
      { label: '已完成', value: 'completed' },
    ],
  },
]

// ConfirmDialog
const showConfirmDialog = ref(false)
const confirmType = ref<'warning' | 'danger'>('warning')

function openDangerDialog() {
  confirmType.value = 'danger'
  showConfirmDialog.value = true
}

function openWarningDialog() {
  confirmType.value = 'warning'
  showConfirmDialog.value = true
}

function handleConfirm() {
  console.log('确认操作')
  showConfirmDialog.value = false
}

function handleSearch(value: string) {
  console.log('搜索:', value)
}

function handleFilter(key: string, value: string) {
  console.log('筛选:', key, value)
}

function handleCreate() {
  console.log('新建')
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 6 - 通用UI增强组件</p>
      </div>

      <!-- CardHeader 卡片标题 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">CardHeader - 卡片标题区域</h2>
          <p class="text-sm text-muted-foreground mt-1">卡片深色标题区域，含状态徽章和更多菜单</p>
        </div>

        <div class="p-4 space-y-4">
          <!-- 示例 1: 基础标题 -->
          <div class="rounded-lg overflow-hidden border border-border">
            <CardHeader title="订单 ORD-20260615-003" status="pending_delivery" />
            <div class="p-4 bg-background">
              <p class="text-sm text-muted-foreground">卡片内容区域</p>
            </div>
          </div>

          <!-- 示例 2: 带菜单 -->
          <div class="rounded-lg overflow-hidden border border-border">
            <CardHeader
              title="报价单 QT-20260618-001"
              subtitle="洛阳轴承集团"
              status="pending"
              :menu-items="headerMenuItems"
              @menu-select="(item) => console.log('菜单:', item)"
            />
            <div class="p-4 bg-background">
              <p class="text-sm text-muted-foreground">卡片内容区域</p>
            </div>
          </div>

          <!-- 示例 3: 自定义标题插槽 -->
          <div class="rounded-lg overflow-hidden border border-border">
            <CardHeader>
              <template #title>
                <div class="flex items-center gap-2">
                  <span class="font-mono text-sm font-semibold text-foreground">ORD-20260610-002</span>
                  <span class="px-2 py-0.5 text-xs bg-blue-500/10 text-blue-500 rounded">紧急</span>
                </div>
              </template>
            </CardHeader>
            <div class="p-4 bg-background">
              <p class="text-sm text-muted-foreground">自定义标题内容</p>
            </div>
          </div>
        </div>
      </div>

      <!-- CopyableNumber 可复制编号 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">CopyableNumber - 可复制编号</h2>
          <p class="text-sm text-muted-foreground mt-1">点击复制图标可复制编号到剪贴板</p>
        </div>

        <div class="p-4 space-y-4">
          <!-- 示例 1: 订单编号 -->
          <div class="flex items-center gap-4 p-3 bg-muted rounded-lg">
            <span class="text-sm text-muted-foreground">订单编号:</span>
            <CopyableNumber value="ORD-20260615-003" />
          </div>

          <!-- 示例 2: 报价单编号 -->
          <div class="flex items-center gap-4 p-3 bg-muted rounded-lg">
            <span class="text-sm text-muted-foreground">报价单号:</span>
            <CopyableNumber value="QT-20260618-001" label="报价单" />
          </div>

          <!-- 示例 3: 物料码 -->
          <div class="flex items-center gap-4 p-3 bg-muted rounded-lg">
            <span class="text-sm text-muted-foreground">物料码:</span>
            <CopyableNumber value="BCR-0001" :show-label="false" />
          </div>
        </div>
      </div>

      <!-- FilterTabs 状态筛选 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">FilterTabs - Tab状态筛选</h2>
          <p class="text-sm text-muted-foreground mt-1">列表页顶部的状态筛选Tab</p>
        </div>

        <div class="p-4 space-y-4">
          <FilterTabs v-model:active-tab="activeTab" :tabs="tabs" />
          <div class="p-3 bg-muted rounded-lg">
            <p class="text-sm text-muted-foreground">当前选中: {{ activeTab }}</p>
          </div>
        </div>
      </div>

      <!-- SearchFilterBar 搜索筛选栏 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">SearchFilterBar - 搜索筛选栏</h2>
          <p class="text-sm text-muted-foreground mt-1">下拉筛选 + 搜索框 + 新建按钮</p>
        </div>

        <div class="p-4 space-y-4">
          <!-- 示例 1: 完整功能 -->
          <div class="border border-border rounded-lg overflow-hidden">
            <SearchFilterBar
              :filters="filters"
              search-placeholder="搜索客户名称、订单编号..."
              :show-create-button="true"
              create-button-text="新建订单"
              @search="handleSearch"
              @filter="handleFilter"
              @create="handleCreate"
            />
            <div class="p-4 bg-background">
              <p class="text-sm text-muted-foreground">列表内容区域</p>
            </div>
          </div>

          <!-- 示例 2: 仅搜索 -->
          <div class="border border-border rounded-lg overflow-hidden">
            <SearchFilterBar
              search-placeholder="搜索..."
              @search="handleSearch"
            />
          </div>
        </div>
      </div>

      <!-- ConfirmDialog 二次确认 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">ConfirmDialog - 二次确认对话框</h2>
          <p class="text-sm text-muted-foreground mt-1">危险操作的二次确认弹窗</p>
        </div>

        <div class="p-4 space-y-4">
          <div class="flex items-center gap-3">
            <button
              class="px-4 py-2 text-sm font-medium bg-yellow-500 text-white rounded-md hover:bg-yellow-600 transition-colors"
              @click="openWarningDialog"
            >
              警告确认
            </button>
            <button
              class="px-4 py-2 text-sm font-medium bg-destructive text-white rounded-md hover:bg-destructive/90 transition-colors"
              @click="openDangerDialog"
            >
              危险确认
            </button>
          </div>

          <ConfirmDialog
            v-model:open="showConfirmDialog"
            :title="confirmType === 'danger' ? '确认删除？' : '确认取消选择？'"
            :description="confirmType === 'danger' ? '删除后无法恢复，请确认操作。' : '已选择 5 个订单项，取消后将清空所有选择。'"
            :type="confirmType"
            :confirm-text="confirmType === 'danger' ? '确认删除' : '确认取消'"
            @confirm="handleConfirm"
          />
        </div>
      </div>

      <!-- 功能特性说明 -->
      <div class="rounded-lg border border-border p-4">
        <h3 class="text-sm font-semibold text-foreground mb-2">Phase 6 组件特性</h3>
        <ul class="text-sm text-muted-foreground space-y-1">
          <li>• <strong>CardHeader</strong>: 卡片标题区域，支持状态徽章和下拉菜单</li>
          <li>• <strong>CopyableNumber</strong>: 可复制编号，点击复制并显示Toast提示</li>
          <li>• <strong>FilterTabs</strong>: Tab状态筛选，支持计数显示</li>
          <li>• <strong>SearchFilterBar</strong>: 搜索筛选栏，支持多筛选条件和新建按钮</li>
          <li>• <strong>ConfirmDialog</strong>: 二次确认对话框，支持警告和危险两种类型</li>
        </ul>
      </div>
    </div>
  </div>
</template>
