# ERP 模板项目

基于 Vue 3 + TypeScript + shadcn-vue 的企业级 ERP 系统模板。

## 特性

- ✅ **Vue 3.5** + **TypeScript 5.7** + **Vite 6**
- ✅ **shadcn-vue** 设计系统（基于 Radix Vue + Tailwind CSS）
- ✅ 完整的设计令牌（oklch 色彩空间）
- ✅ 业务组件封装（PageHeader、DataTable）
- ✅ 参考页面模板（列表页、表单页、详情页）
- ✅ AI 开发规范（CLAUDE.md）
- ✅ 支持亮色/暗色主题

## 快速开始

```bash
# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev

# 构建
pnpm build
```

## 项目结构

```
src/
├── components/
│   ├── ui/                    # shadcn 组件（禁止修改）
│   │   ├── button/
│   │   ├── input/
│   │   ├── card/
│   │   ├── table/
│   │   └── label/
│   ├── PageHeader.vue         # 业务组件：页面标题
│   └── DataTable.vue          # 业务组件：数据表格
├── pages/
│   └── reference/             # 参考页面（AI 参考标准）
│       ├── list-page.example.vue
│       ├── form-page.example.vue
│       └── detail-page.example.vue
├── views/                     # 实际页面
├── router/                    # 路由配置
├── assets/
│   └── main.css               # 全局样式（含设计令牌）
├── App.vue
└── main.ts
```

## 使用指南

### 创建列表页

参考 `src/pages/reference/list-page.example.vue`：

```vue
<script setup lang="ts">
import PageHeader from '@/components/PageHeader.vue'
import DataTable from '@/components/DataTable.vue'
import { Button } from '@/components/ui/button'

const columns = [
  { key: 'id', title: 'ID' },
  { key: 'name', title: '名称' },
]

const data = [
  { id: 1, name: '示例' },
]
</script>

<template>
  <div class="container max-w-[1280px] mx-auto p-6">
    <PageHeader title="列表页" description="描述">
      <template #actions>
        <Button>新增</Button>
      </template>
    </PageHeader>
    <DataTable :columns="columns" :data="data" />
  </div>
</template>
```

### 创建表单页

参考 `src/pages/reference/form-page.example.vue`：

```vue
<script setup lang="ts">
import PageHeader from '@/components/PageHeader.vue'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Label } from '@/components/ui/label'
</script>

<template>
  <div class="container max-w-[1280px] mx-auto p-6">
    <PageHeader title="表单页" description="描述" />
    <Card>
      <CardHeader>
        <CardTitle>表单标题</CardTitle>
      </CardHeader>
      <CardContent class="space-y-4">
        <div class="space-y-2">
          <Label>字段名</Label>
          <Input placeholder="请输入" />
        </div>
        <div class="flex gap-2 pt-4">
          <Button>提交</Button>
          <Button variant="outline">取消</Button>
        </div>
      </CardContent>
    </Card>
  </div>
</template>
```

## 设计系统

### 颜色

使用 shadcn 语义化颜色，基于 oklch 色彩空间：

```css
--primary: oklch(0.841 0.238 128.85);  /* 主色：绿色 */
--background: oklch(1 0 0);            /* 背景色 */
--foreground: oklch(0.145 0 0);        /* 前景色 */
--border: oklch(0.922 0 0);            /* 边框色 */
```

### 使用方式

```vue
<!-- ✅ 正确 -->
<div class="bg-primary text-primary-foreground border-border">
  内容
</div>

<!-- ❌ 错误 -->
<div class="bg-blue-500 text-white border-gray-200">
  内容
</div>
```

## AI 开发规范

查看 `CLAUDE.md` 了解完整的 AI 开发规范。

### 核心原则

1. **参考优先**：创建新页面前，必须先查看对应的参考页面
2. **使用组件**：优先使用封装的业务组件和 shadcn 组件
3. **遵循规范**：使用设计令牌，禁止硬编码值
4. **检查清单**：每个页面完成后，必须验证检查清单

## 技术栈

- **框架**: Vue 3.5 + TypeScript 5.7
- **构建工具**: Vite 6
- **UI 库**: shadcn-vue（Radix Vue + Tailwind CSS）
- **路由**: Vue Router 4
- **状态管理**: Pinia 2
- **包管理器**: pnpm
- **图标**: @lucide/vue

## License

MIT
