<script setup lang="ts">
import { computed } from 'vue'
import { Card, CardContent } from '@/components/ui/card'
import { TrendingUp, TrendingDown, Package, DollarSign, Users, ShoppingCart, FileText, CheckCircle } from '@lucide/vue'

export type StatIcon = 'package' | 'dollar' | 'users' | 'orders' | 'quotation' | 'check'

export interface StatData {
  title: string
  value: number | string
  change?: number // 百分比变化，正数为增长，负数为下降
  icon: StatIcon
  color?: 'primary' | 'green' | 'blue' | 'purple' | 'orange'
}

interface Props {
  stat: StatData
}

const props = withDefaults(defineProps<Props>(), {
  color: 'primary',
})

// 图标映射
const iconMap = {
  package: Package,
  dollar: DollarSign,
  users: Users,
  orders: ShoppingCart,
  quotation: FileText,
  check: CheckCircle,
}

// 颜色映射
const colorClasses = {
  primary: 'bg-primary/10 text-primary',
  green: 'bg-green-100 text-green-600 dark:bg-green-900/30 dark:text-green-400',
  blue: 'bg-blue-100 text-blue-600 dark:bg-blue-900/30 dark:text-blue-400',
  purple: 'bg-purple-100 text-purple-600 dark:bg-purple-900/30 dark:text-purple-400',
  orange: 'bg-orange-100 text-orange-600 dark:bg-orange-900/30 dark:text-orange-400',
}

const iconColorClass = computed(() => {
  return colorClasses[props.stat.color || 'primary']
})
</script>

<template>
  <Card>
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
        <div class="flex-shrink-0">
          <div
            class="h-12 w-12 rounded-lg flex items-center justify-center"
            :class="iconColorClass"
          >
            <component
              :is="iconMap[stat.icon]"
              class="h-6 w-6"
            />
          </div>
        </div>
      </div>
    </CardContent>
  </Card>
</template>
