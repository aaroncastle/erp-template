<script setup lang="ts">
interface Props {
  showZoneA?: boolean
  showZoneB?: boolean
  showZoneC?: boolean
  showZoneD?: boolean
  showZoneE?: boolean
  showZoneF?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  showZoneA: true,
  showZoneB: true,
  showZoneC: true,
  showZoneD: true,
  showZoneE: true,
  showZoneF: true,
})
</script>

<template>
  <div class="zone-layout grid grid-cols-[300px_1fr_300px] gap-4 min-h-[calc(100vh-200px)]">
    <!-- 左栏 -->
    <div class="flex flex-col gap-4">
      <!-- A区：项目预警 -->
      <div v-if="showZoneA" class="zone-a">
        <slot name="zone-a" />
      </div>

      <!-- B区：功能筛选 -->
      <div v-if="showZoneB" class="zone-b">
        <slot name="zone-b" />
      </div>

      <!-- C区：卡片列表 -->
      <div v-if="showZoneC" class="zone-c flex-1 overflow-y-auto">
        <slot name="zone-c" />
      </div>
    </div>

    <!-- 中栏 -->
    <div class="flex flex-col">
      <!-- D区：详情面板 -->
      <div v-if="showZoneD" class="zone-d flex-1">
        <slot name="zone-d" />
      </div>
    </div>

    <!-- 右栏 -->
    <div class="flex flex-col gap-4">
      <!-- E区：搜索功能栏 -->
      <div v-if="showZoneE" class="zone-e">
        <slot name="zone-e" />
      </div>

      <!-- F区：通知面板 -->
      <div v-if="showZoneF" class="zone-f flex-1 overflow-y-auto">
        <slot name="zone-f" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.zone-layout {
  min-height: 600px;
}
</style>
