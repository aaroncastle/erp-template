<script setup lang="ts">
import { ref } from 'vue'
import NumberInput from '@/components/business/NumberInput.vue'
import DateRangePicker from '@/components/business/DateRangePicker.vue'

// NumberInput 数据
const numberValue1 = ref<number | null>(10)
const numberValue2 = ref<number | null>(1500)
const numberValue3 = ref<number | null>(50)
const numberValue4 = ref<number | null>(null)

// DateRangePicker 数据
const dateRange1 = ref<[string, string] | null>(null)
const dateRange2 = ref<[string, string] | null>(['2026-06-01', '2026-06-15'])

function handleNumberChange(value: number | null) {
  console.log('数字变化:', value)
}

function handleDateRangeChange(value: [string, string] | null) {
  console.log('日期范围变化:', value)
}
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <div class="max-w-4xl mx-auto space-y-8">
      <!-- 页面标题 -->
      <div class="space-y-2">
        <h1 class="text-2xl font-bold text-foreground">组件预览</h1>
        <p class="text-muted-foreground">Phase 8 - 表单增强组件</p>
      </div>

      <!-- NumberInput 数字输入框 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">NumberInput - 数字输入框</h2>
          <p class="text-sm text-muted-foreground mt-1">支持步进、精度控制、前后缀</p>
        </div>

        <div class="p-4 space-y-4">
          <!-- 示例 1: 基础数字输入 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">数量（带控制按钮）</label>
            <div class="max-w-xs">
              <NumberInput
                v-model="numberValue1"
                :min="0"
                :max="100"
                :step="1"
                :precision="0"
                placeholder="请输入数量"
                @change="handleNumberChange"
              />
            </div>
            <p class="text-xs text-muted-foreground">当前值: {{ numberValue1 }}</p>
          </div>

          <!-- 示例 2: 金额输入 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">金额（带前后缀）</label>
            <div class="max-w-xs">
              <NumberInput
                v-model="numberValue2"
                :min="0"
                :step="100"
                :precision="2"
                prefix="¥"
                suffix="元"
                placeholder="请输入金额"
                @change="handleNumberChange"
              />
            </div>
            <p class="text-xs text-muted-foreground">当前值: {{ numberValue2 }}</p>
          </div>

          <!-- 示例 3: 百分比输入 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">百分比（无控制按钮）</label>
            <div class="max-w-xs">
              <NumberInput
                v-model="numberValue3"
                :min="0"
                :max="100"
                :step="0.1"
                :precision="1"
                suffix="%"
                :controls="false"
                placeholder="请输入百分比"
                @change="handleNumberChange"
              />
            </div>
            <p class="text-xs text-muted-foreground">当前值: {{ numberValue3 }}%</p>
          </div>

          <!-- 示例 4: 空值输入 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">空值状态</label>
            <div class="max-w-xs">
              <NumberInput
                v-model="numberValue4"
                :min="0"
                :max="1000"
                :precision="2"
                placeholder="未设置"
                @change="handleNumberChange"
              />
            </div>
            <p class="text-xs text-muted-foreground">当前值: {{ numberValue4 ?? 'null' }}</p>
          </div>

          <!-- 示例 5: 禁用状态 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">禁用状态</label>
            <div class="max-w-xs">
              <NumberInput
                :model-value="50"
                :disabled="true"
                :controls="true"
              />
            </div>
          </div>
        </div>
      </div>

      <!-- DateRangePicker 日期范围选择器 -->
      <div class="rounded-lg border border-border">
        <div class="p-4 border-b border-border">
          <h2 class="text-lg font-semibold text-foreground">DateRangePicker - 日期范围选择器</h2>
          <p class="text-sm text-muted-foreground mt-1">选择开始和结束日期</p>
        </div>

        <div class="p-4 space-y-4">
          <!-- 示例 1: 基础日期范围 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">选择日期范围</label>
            <div class="max-w-sm">
              <DateRangePicker
                v-model="dateRange1"
                placeholder="请选择日期范围"
                @change="handleDateRangeChange"
              />
            </div>
            <p class="text-xs text-muted-foreground">
              当前值: {{ dateRange1 ? `${dateRange1[0]} ~ ${dateRange1[1]}` : '未选择' }}
            </p>
          </div>

          <!-- 示例 2: 预设值 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">预设日期范围</label>
            <div class="max-w-sm">
              <DateRangePicker
                v-model="dateRange2"
                placeholder="请选择日期范围"
                @change="handleDateRangeChange"
              />
            </div>
            <p class="text-xs text-muted-foreground">
              当前值: {{ dateRange2 ? `${dateRange2[0]} ~ ${dateRange2[1]}` : '未选择' }}
            </p>
          </div>

          <!-- 示例 3: 禁用状态 -->
          <div class="space-y-2">
            <label class="text-sm font-medium text-foreground">禁用状态</label>
            <div class="max-w-sm">
              <DateRangePicker
                :model-value="['2026-01-01', '2026-12-31']"
                :disabled="true"
              />
            </div>
          </div>
        </div>
      </div>

      <!-- 功能特性说明 -->
      <div class="rounded-lg border border-border p-4">
        <h3 class="text-sm font-semibold text-foreground mb-2">Phase 8 组件特性</h3>
        <ul class="text-sm text-muted-foreground space-y-1">
          <li>• <strong>NumberInput</strong>: 数字输入框，支持步进控制、精度控制、前后缀、键盘操作</li>
          <li>• <strong>DateRangePicker</strong>: 日期范围选择器，支持日历选择、清空、月份切换</li>
        </ul>
      </div>
    </div>
  </div>
</template>
