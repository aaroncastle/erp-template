<script setup lang="ts">
import PageHeader from '@/components/PageHeader.vue'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Button } from '@/components/ui/button'
</script>

<template>
  <div class="container max-w-[1280px] mx-auto p-6">
    <!-- 页面标题 -->
    <PageHeader title="用户详情" description="查看用户详细信息">
      <template #actions>
        <Button>编辑</Button>
        <Button variant="destructive">删除</Button>
      </template>
    </PageHeader>

    <!-- 详情卡片 -->
    <Card>
      <CardHeader>
        <CardTitle>基本信息</CardTitle>
      </CardHeader>
      <CardContent class="space-y-4">
        <div class="grid grid-cols-2 gap-4">
          <div>
            <p class="text-sm text-muted-foreground">姓名</p>
            <p class="text-base font-medium">张三</p>
          </div>
          <div>
            <p class="text-sm text-muted-foreground">邮箱</p>
            <p class="text-base font-medium">zhangsan@example.com</p>
          </div>
          <div>
            <p class="text-sm text-muted-foreground">角色</p>
            <p class="text-base font-medium">管理员</p>
          </div>
          <div>
            <p class="text-sm text-muted-foreground">创建时间</p>
            <p class="text-base font-medium">2024-01-15 10:30</p>
          </div>
        </div>
      </CardContent>
    </Card>
  </div>
</template>
