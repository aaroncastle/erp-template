<script setup lang="ts">
import { Copy, Check, Eye } from '@lucide/vue'
import { ref } from 'vue'
import StatusBadge from './StatusBadge.vue'

export interface OrderItem {
  id: string
  number: string
  productName: string
  specification: string
  quantity: number
  unitPrice: number
  amount: number
  status: string
}

interface Props {
  item: OrderItem
  dualItems?: OrderItem[] // 客户指定项（可以有多个）
  isDuality?: boolean // 是否是阴阳项目
}

const props = withDefaults(defineProps<Props>(), {
  dualItems: () => [],
  isDuality: false,
})

const emit = defineEmits<{
  (e: 'click', item: OrderItem): void
  (e: 'view-dual', item: OrderItem, dualItems: OrderItem[]): void
}>()

const copiedNumber = ref<string | null>(null)
const showPanel = ref(false)
const badgeTop = ref(0)
const badgeRight = ref(0)

async function copyNumber(number: string) {
  try {
    await navigator.clipboard.writeText(number)
    copiedNumber.value = number
    setTimeout(() => {
      copiedNumber.value = null
    }, 2000)
  } catch (err) {
    console.error('复制失败:', err)
  }
}

function handleMouseEnter(event: Event) {
  const button = event.currentTarget as HTMLElement
  const rect = button.getBoundingClientRect()
  badgeTop.value = rect.bottom
  badgeRight.value = window.innerWidth - rect.right
  showPanel.value = true
}

function handleMouseLeave() {
  showPanel.value = false
}
</script>

<template>
  <div
    class="relative rounded-lg border border-border bg-background cursor-pointer hover:shadow-md transition-shadow"
    @click="emit('click', item)"
  >
    <!-- 卡片标题区域 -->
    <div class="bg-muted p-3 border-b border-border">
      <div class="flex items-center justify-between gap-2">
        <span class="text-sm font-semibold text-foreground truncate">
          {{ item.productName }}
        </span>
        <div class="flex items-center gap-2 flex-shrink-0">
          <!-- 客户指定徽章 -->
          <div
            v-if="isDuality && dualItems.length > 0"
            class="relative"
          >
            <button
              class="inline-flex items-center gap-1 px-2 py-1 text-xs font-medium rounded-md bg-purple-100 text-purple-800 dark:bg-purple-900/30 dark:text-purple-300 hover:bg-purple-200 dark:hover:bg-purple-900/50 transition-colors"
              @click.stop="emit('view-dual', item, dualItems)"
              @mouseenter="handleMouseEnter"
              @mouseleave="handleMouseLeave"
            >
              <Eye class="h-3 w-3" />
              指定项 ({{ dualItems.length }})
            </button>

            <!-- Hover 显示指定项列表 - 使用 Teleport 避免被父容器裁剪 -->
            <Teleport to="body">
              <div
                v-if="showPanel"
                class="absolute mt-1 w-64 bg-background border border-border rounded-lg shadow-xl z-50"
                :style="{ top: badgeTop + 'px', right: badgeRight + 'px' }"
                @mouseenter="showPanel = true"
                @mouseleave="showPanel = false"
              >
                <div class="max-h-96 overflow-y-auto scrollbar-hide">
                  <div
                    v-for="(dualItem, index) in dualItems"
                    :key="dualItem.id"
                    class="p-3 border-b border-border last:border-b-0"
                  >
                    <div class="space-y-2">
                      <!-- 产品名称 -->
                      <div>
                        <span class="text-sm font-medium text-foreground">{{ dualItem.productName }}</span>
                      </div>

                      <!-- 数量 -->
                      <div class="text-sm">
                        <span class="text-muted-foreground">数量：</span>
                        <span class="text-foreground">{{ dualItem.quantity }}</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </Teleport>
          </div>

          <!-- 状态徽章 -->
          <StatusBadge :status="item.status as any" />
        </div>
      </div>
    </div>

    <!-- 卡片内容 -->
    <div class="p-3 space-y-2">
      <!-- 编号 -->
      <div class="flex items-center justify-between">
        <span class="text-xs text-muted-foreground">编号：</span>
        <div class="flex items-center gap-2">
          <span class="text-sm font-medium text-foreground">{{ item.number }}</span>
          <button
            class="text-muted-foreground hover:text-foreground transition-colors"
            @click.stop="copyNumber(item.number)"
          >
            <Check v-if="copiedNumber === item.number" class="h-3.5 w-3.5 text-green-500" />
            <Copy v-else class="h-3.5 w-3.5" />
          </button>
        </div>
      </div>

      <!-- 规格 -->
      <div class="flex items-center justify-between">
        <span class="text-xs text-muted-foreground">规格：</span>
        <span class="text-sm text-foreground">{{ item.specification }}</span>
      </div>

      <!-- 数量 -->
      <div class="flex items-center justify-between">
        <span class="text-xs text-muted-foreground">数量：</span>
        <span class="text-sm font-medium text-foreground">{{ item.quantity }}</span>
      </div>

      <!-- 单价 -->
      <div class="flex items-center justify-between">
        <span class="text-xs text-muted-foreground">单价：</span>
        <span class="text-sm text-foreground">¥{{ item.unitPrice.toFixed(2) }}</span>
      </div>

      <!-- 金额 -->
      <div class="flex items-center justify-between pt-2 border-t border-border">
        <span class="text-xs font-medium text-muted-foreground">金额：</span>
        <span class="text-sm font-semibold text-foreground">¥{{ item.amount.toFixed(2) }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* 隐藏滚动条但保持滚动功能 */
.scrollbar-hide::-webkit-scrollbar {
  display: none;
}
.scrollbar-hide {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>
