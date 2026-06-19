<script setup lang="ts">
import { ref } from 'vue'

// Phase 2 组件导入
import ZoneLayout from '@/components/business/ZoneLayout.vue'
import ProjectWarning from '@/components/business/ProjectWarning.vue'
import FunctionFilter from '@/components/business/FunctionFilter.vue'
import DetailPanel from '@/components/business/DetailPanel.vue'
import SearchFunctionBar from '@/components/business/SearchFunctionBar.vue'
import NotificationPanel from '@/components/business/NotificationPanel.vue'

// Phase 2 模拟数据
const showWarning = ref(true)
const selectedModule = ref<'quotation' | 'order'>('order')
const selectedStatus = ref('all')
const searchQuery = ref('')
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-7xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 2 - Zone布局组件</p>
      </div>

      <!-- Phase 2 组件预览 -->

      <!-- Zone布局演示 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <div>
          <h2 class="text-lg font-semibold text-foreground">Zone布局引擎</h2>
          <p class="text-sm text-muted-foreground mt-1">
            动态布局渲染，支持A-F六个区域
          </p>
        </div>

        <ZoneLayout>
          <!-- A区：项目预警 -->
          <template #zone-a>
            <ProjectWarning
              v-model:show="showWarning"
              type="warning"
              message="您有3个订单即将逾期"
            />
          </template>

          <!-- B区：功能筛选 -->
          <template #zone-b>
            <FunctionFilter
              v-model:module="selectedModule"
              v-model:status="selectedStatus"
              :show-manager-filter="true"
            />
          </template>

          <!-- C区：卡片列表（占位） -->
          <template #zone-c>
            <div class="p-4 border border-border rounded-lg bg-muted/30">
              <p class="text-sm text-muted-foreground">C区：卡片列表区域</p>
            </div>
          </template>

          <!-- D区：详情面板（占位） -->
          <template #zone-d>
            <DetailPanel title="详情面板">
              <p class="text-sm text-muted-foreground">D区：选中卡片后显示详情</p>
            </DetailPanel>
          </template>

          <!-- E区：搜索功能栏 -->
          <template #zone-e>
            <SearchFunctionBar
              v-model:search="searchQuery"
              placeholder="搜索订单..."
            />
          </template>

          <!-- F区：通知面板 -->
          <template #zone-f>
            <NotificationPanel :unread-count="5" />
          </template>
        </ZoneLayout>
      </div>

      <!-- 组件说明 -->
      <div class="rounded-lg border border-border p-6 space-y-4">
        <h2 class="text-lg font-semibold text-foreground">组件说明</h2>
        <div class="text-sm text-muted-foreground space-y-2">
          <p><strong>A区（项目预警）：</strong>显示预警提示，可关闭</p>
          <p><strong>B区（功能筛选）：</strong>模块切换、状态筛选、经理筛选</p>
          <p><strong>C区（卡片列表）：</strong>显示订单/报价单卡片列表</p>
          <p><strong>D区（详情面板）：</strong>显示选中卡片的详情</p>
          <p><strong>E区（搜索功能栏）：</strong>搜索框+固定功能按钮</p>
          <p><strong>F区（通知面板）：</strong>通知列表+未读徽章</p>
        </div>
      </div>
    </div>
  </div>
</template>
