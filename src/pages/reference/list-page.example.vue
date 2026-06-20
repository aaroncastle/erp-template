<script setup lang="ts">
import PageHeader from '@/components/PageHeader.vue'
import DataTable from '@/components/DataTable.vue'
import { Button } from '@/components/ui/button'

// 定义数据类型
interface User {
  id: number
  name: string
  email: string
  role: string
}

// 定义列配置
const columns = [
  { key: 'id' as const, title: 'ID' },
  { key: 'name' as const, title: '姓名' },
  { key: 'email' as const, title: '邮箱' },
  { key: 'role' as const, title: '角色' },
]

// 模拟数据
const mockData: User[] = [
  { id: 1, name: '张三', email: 'zhangsan@example.com', role: '管理员' },
  { id: 2, name: '李四', email: 'lisi@example.com', role: '普通用户' },
  { id: 3, name: '王五', email: 'wangwu@example.com', role: '普通用户' },
]
</script>

<template>
  <div class="container max-w-[1280px] mx-auto p-6">
    <!-- 页面标题 -->
    <PageHeader
      title="用户列表"
      description="管理系统中的所有用户"
    >
      <template #actions>
        <Button>新增用户</Button>
      </template>
    </PageHeader>

    <!-- 数据表格 -->
    <DataTable
      :columns="columns"
      :data="mockData"
      search-key="name"
    />
  </div>
</template>
