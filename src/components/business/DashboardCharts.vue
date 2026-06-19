<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { TrendingUp, TrendingDown, Package, DollarSign, Users, ShoppingCart } from '@lucide/vue'

export interface ChartData {
  label: string
  value: number
  color?: string
}

export interface StatCard {
  title: string
  value: number | string
  change?: number // 百分比变化，正数为增长，负数为下降
  icon: 'package' | 'dollar' | 'users' | 'orders'
}

interface Props {
  stats?: StatCard[]
  orderStatusChart?: ChartData[]
  amountTrendChart?: ChartData[]
  departmentPerformance?: ChartData[]
}

const props = withDefaults(defineProps<Props>(), {
  stats: () => [],
  orderStatusChart: () => [],
  amountTrendChart: () => [],
  departmentPerformance: () => [],
})

// 图标映射
const iconMap = {
  package: Package,
  dollar: DollarSign,
  users: Users,
  orders: ShoppingCart,
}

// 计算最大值用于图表比例
const maxOrderStatus = computed(() => {
  if (props.orderStatusChart.length === 0) return 1
  return Math.max(...props.orderStatusChart.map(d => d.value))
})

const maxAmountTrend = computed(() => {
  if (props.amountTrendChart.length === 0) return 1
  return Math.max(...props.amountTrendChart.map(d => d.value))
})

const maxDepartment = computed(() => {
  if (props.departmentPerformance.length === 0) return 1
  return Math.max(...props.departmentPerformance.map(d => d.value))
})
</script>

<template>
  <div class="space-y-6">
    <!-- 统计卡片 -->
    <div v-if="stats.length > 0" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
      <Card v-for="(stat, index) in stats" :key="index">
        <CardContent class="p-4">
          <div class="flex items-center justify-between">
            <div class="space-y-1">
              <p class="text-sm text-muted-foreground">{{ stat.title }}</p>
              <p class="text-2xl font-bold text-foreground">{{ stat.value }}</p>
              <div v-if="stat.change !== undefined" class="flex items-center gap-1">
                <component
                  :is="stat.change >= 0 ? TrendingUp : TrendingDown"
                  class="h-3 w-3"
                  :class="stat.change >= 0 ? 'text-green-600' : 'text-red-600'"
                />
                <span
                  class="text-xs font-medium"
                  :class="stat.change >= 0 ? 'text-green-600' : 'text-red-600'"
                >
                  {{ Math.abs(stat.change) }}%
                </span>
              </div>
            </div>
            <component
              :is="iconMap[stat.icon]"
              class="h-10 w-10 text-muted-foreground"
            />
          </div>
        </CardContent>
      </Card>
    </div>

    <!-- 图表区域 -->
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
      <!-- 订单状态分布 -->
      <Card v-if="orderStatusChart.length > 0">
        <CardHeader>
          <CardTitle>订单状态分布</CardTitle>
        </CardHeader>
        <CardContent>
          <div class="space-y-3">
            <div
              v-for="(item, index) in orderStatusChart"
              :key="index"
              class="space-y-1"
            >
              <div class="flex items-center justify-between text-sm">
                <span class="text-foreground">{{ item.label }}</span>
                <span class="font-medium text-foreground">{{ item.value }}</span>
              </div>
              <div class="h-2 rounded-full bg-muted overflow-hidden">
                <div
                  class="h-full rounded-full transition-all duration-300"
                  :style="{
                    width: `${(item.value / maxOrderStatus) * 100}%`,
                    backgroundColor: item.color || 'var(--primary)',
                  }"
                />
              </div>
            </div>
          </div>
        </CardContent>
      </Card>

      <!-- 金额趋势 -->
      <Card v-if="amountTrendChart.length > 0">
        <CardHeader>
          <CardTitle>金额趋势</CardTitle>
        </CardHeader>
        <CardContent>
          <div class="space-y-3">
            <div
              v-for="(item, index) in amountTrendChart"
              :key="index"
              class="space-y-1"
            >
              <div class="flex items-center justify-between text-sm">
                <span class="text-foreground">{{ item.label }}</span>
                <span class="font-medium text-primary">¥{{ item.value.toLocaleString() }}</span>
              </div>
              <div class="h-2 rounded-full bg-muted overflow-hidden">
                <div
                  class="h-full rounded-full bg-primary transition-all duration-300"
                  :style="{
                    width: `${(item.value / maxAmountTrend) * 100}%`,
                  }"
                />
              </div>
            </div>
          </div>
        </CardContent>
      </Card>

      <!-- 部门业绩对比 -->
      <Card v-if="departmentPerformance.length > 0" class="lg:col-span-2">
        <CardHeader>
          <CardTitle>部门业绩对比</CardTitle>
        </CardHeader>
        <CardContent>
          <div class="space-y-3">
            <div
              v-for="(item, index) in departmentPerformance"
              :key="index"
              class="space-y-1"
            >
              <div class="flex items-center justify-between text-sm">
                <span class="text-foreground">{{ item.label }}</span>
                <span class="font-medium text-primary">¥{{ item.value.toLocaleString() }}</span>
              </div>
              <div class="h-3 rounded-full bg-muted overflow-hidden">
                <div
                  class="h-full rounded-full transition-all duration-300"
                  :style="{
                    width: `${(item.value / maxDepartment) * 100}%`,
                    backgroundColor: item.color || 'var(--primary)',
                  }"
                />
              </div>
            </div>
          </div>
        </CardContent>
      </Card>
    </div>
  </div>
</template>
