## 前端设计系统模板

本项目是前端设计系统模板，提供设计令牌、布局规范和样式参考组件。任何项目都可以集成使用。

### 技术栈

- Vue 3.5 + TypeScript 5.7
- shadcn-vue（基于 Radix Vue + Tailwind CSS）
- Vue Router 4 / Pinia 2
- Vite 6 / pnpm
- @lucide/vue 图标

### 何时参考本模板

AI 仅在以下任务时需要 Read 本文档及关联文件：

- 创建或修改 Vue 页面
- 创建或修改业务组件
- 规划前端阶段的 UI 结构

**其他任务（后端、数据库、部署等）不需要参考。**

### 关联文档

- `DESIGN_SYSTEM.md` — 设计令牌、布局模式、开发约束
- `COMPONENT_REFERENCE.md` — 每个组件的用途和参考价值说明

### 快速规则

1. 用 shadcn 基础组件直接组合页面
2. 编号字段旁必须有复制按钮
3. 卡片：标题区（bg-muted）+ 内容区 + 操作区
4. 详情：右侧抽屉，左侧 60% 列表，右侧 40% 操作
5. 禁止硬编码颜色、间距；禁止 Tailwind 默认颜色
