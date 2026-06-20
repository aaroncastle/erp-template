<script setup lang="ts">
import PageHeader from '@/components/PageHeader.vue'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Label } from '@/components/ui/label'
</script>

<template>
  <div class="container max-w-[1280px] mx-auto p-6">
    <!-- 页面标题 -->
    <PageHeader
      title="新增用户"
      description="填写用户信息以创建新账户"
    />

    <!-- 表单卡片 -->
    <Card>
      <CardHeader>
        <CardTitle>基本信息</CardTitle>
      </CardHeader>
      <CardContent class="space-y-4">
        <!-- 姓名字段 -->
        <div class="space-y-2">
          <Label>姓名</Label>
          <Input placeholder="请输入姓名" />
        </div>

        <!-- 邮箱字段 -->
        <div class="space-y-2">
          <Label>邮箱</Label>
          <Input type="email" placeholder="请输入邮箱" />
        </div>

        <!-- 角色字段 -->
        <div class="space-y-2">
          <Label>角色</Label>
          <Input placeholder="请输入角色" />
        </div>

        <!-- 提交按钮 -->
        <div class="flex gap-2 pt-4">
          <Button>提交</Button>
          <Button variant="outline">取消</Button>
        </div>
      </CardContent>
    </Card>
  </div>
</template>
