# ERP 模板仓库搭建完成总结

## ✅ 已完成的工作

### 1. 基础项目初始化
- [x] Vue 3.5 + TypeScript 5.7 + Vite 6
- [x] Vue Router 4 + Pinia 2
- [x] ESLint + Prettier 配置
- [x] pnpm 包管理器

### 2. shadcn-vue 设计系统
- [x] 手动创建 shadcn-vue 组件（避免网络问题）
  - Button（6 种变体）
  - Input
  - Card（Card/CardHeader/CardTitle/CardContent）
  - Table（完整表格组件）
  - Label
- [x] 应用 oklch 色彩空间主题
- [x] 支持亮色/暗色主题切换
- [x] Tailwind CSS 配置完成

### 3. 业务组件封装
- [x] PageHeader：页面标题和操作按钮
- [x] DataTable：数据表格（含搜索功能）

### 4. 参考页面模板
- [x] list-page.example.vue：列表页标准
- [x] form-page.example.vue：表单页标准
- [x] detail-page.example.vue：详情页标准

### 5. 开发规范文档
- [x] CLAUDE.md：AI 开发规范（核心）
- [x] README.md：项目说明文档

### 6. 验证
- [x] 开发服务器启动成功（http://localhost:5173）
- [x] 所有组件导入正确
- [x] 设计令牌生效

## 📁 项目结构

```
erp-template/
├── src/
│   ├── components/
│   │   ├── ui/                    # shadcn 组件（禁止修改）
│   │   │   ├── button/
│   │   │   ├── input/
│   │   │   ├── card/
│   │   │   ├── table/
│   │   │   └── label/
│   │   ├── PageHeader.vue         # 业务组件
│   │   └── DataTable.vue          # 业务组件
│   ├── pages/
│   │   └── reference/             # 参考页面
│   │       ├── list-page.example.vue
│   │       ├── form-page.example.vue
│   │       └── detail-page.example.vue
│   ├── views/
│   │   └── HomeView.vue
│   ├── router/
│   │   └── index.ts
│   ├── lib/
│   │   └── utils.ts               # cn() 工具函数
│   ├── assets/
│   │   └── main.css               # 全局样式（含设计令牌）
│   ├── App.vue
│   ├── main.ts
│   └── env.d.ts
├── CLAUDE.md                      # AI 开发规范
├── README.md                      # 项目说明
├── components.json                # shadcn 配置
├── tailwind.config.js
├── postcss.config.js
├── tsconfig.json
├── tsconfig.node.json
├── vite.config.ts
├── package.json
├── pnpm-lock.yaml
└── .prettierrc.json
```

## 🎨 设计系统

### 颜色令牌（oklch 色彩空间）

```css
/* 主色：绿色系 */
--primary: oklch(0.841 0.238 128.85);
--primary-foreground: oklch(0.405 0.101 131.063);

/* 背景色 */
--background: oklch(1 0 0);
--foreground: oklch(0.145 0 0);

/* 卡片 */
--card: oklch(1 0 0);
--card-foreground: oklch(0.145 0 0);

/* 边框 */
--border: oklch(0.922 0 0);

/* 圆角 */
--radius: 0.625rem;
```

### 使用方式

```vue
<!-- ✅ 正确：使用语义化颜色 -->
<Button class="bg-primary text-primary-foreground">
  按钮
</Button>

<!-- ❌ 错误：硬编码颜色 -->
<Button class="bg-blue-500 text-white">
  按钮
</Button>
```

## 🚀 使用方法

### 1. 从模板创建新项目

```bash
# 方式 1：GitHub CLI
gh repo create new-project --template your-username/erp-template --clone

# 方式 2：手动复制
# 在 GitHub 页面点击 "Use this template"
```

### 2. 安装依赖

```bash
cd new-project
pnpm install
pnpm approve-builds esbuild vue-demi
```

### 3. 开发

```bash
pnpm dev
```

### 4. 创建新页面

参考 `src/pages/reference/` 中的示例页面：

- 列表页 → 参考 `list-page.example.vue`
- 表单页 → 参考 `form-page.example.vue`
- 详情页 → 参考 `detail-page.example.vue`

## 📋 AI 开发规范（CLAUDE.md）

### 核心原则

1. **参考优先**：创建新页面前，必须先查看对应的参考页面
2. **使用组件**：优先使用封装的业务组件和 shadcn 组件
3. **遵循规范**：使用设计令牌，禁止硬编码值
4. **检查清单**：每个页面完成后，必须验证检查清单

### 禁止事项

- ❌ 禁止修改 `src/components/ui/` 中的 shadcn 组件
- ❌ 禁止硬编码颜色、间距等值
- ❌ 禁止重复实现已有组件
- ❌ 禁止自由发挥页面结构
- ❌ 禁止使用 Tailwind 默认颜色类

### 检查清单

- [ ] 使用了 shadcn 语义化颜色？
- [ ] 使用了标准 Tailwind 间距？
- [ ] 使用了封装的组件（PageHeader/DataTable 等）？
- [ ] 参考了对应的 example 页面？
- [ ] 响应式布局正确？
- [ ] 加载状态有骨架屏？
- [ ] 空状态有提示？
- [ ] 错误状态有反馈？

## 🎯 下一步

### 推送到 GitHub

```bash
cd /Volumes/tdd/github/erp-template

# 添加所有文件
git add .

# 提交
git commit -m "feat: 初始化 ERP 模板仓库

- Vue 3.5 + TypeScript + shadcn-vue
- 完整的设计系统（oklch 色彩空间）
- 业务组件（PageHeader、DataTable）
- 参考页面模板（列表页、表单页、详情页）
- AI 开发规范（CLAUDE.md）"

# 推送
git push -u origin main
```

### 扩展组件

根据项目需要，可以继续添加 shadcn-vue 组件：

```bash
# 手动添加组件（推荐，避免网络问题）
# 参考已创建的 Button、Input 等组件格式

# 或使用 CLI（需要良好网络）
pnpm dlx shadcn-vue@latest add dialog
pnpm dlx shadcn-vue@latest add form
pnpm dlx shadcn-vue@latest add select
```

### 扩展参考页面

根据业务需要，可以添加更多参考页面：

- dashboard-page.example.vue：仪表盘页面
- settings-page.example.vue：设置页面
- profile-page.example.vue：个人资料页面

## 📝 注意事项

1. **网络问题**：shadcn-vue CLI 可能因网络问题超时，建议手动创建组件
2. **构建脚本**：安装依赖后需要运行 `pnpm approve-builds esbuild vue-demi`
3. **组件更新**：更新模板后，已有项目需要手动同步
4. **主题定制**：修改主题色时，需要同时更新 `src/assets/main.css` 和 `tailwind.config.js`

## 🎉 总结

现在你有了一个完整的 ERP 模板仓库：

- ✅ 基于 shadcn-vue 的设计系统
- ✅ 完整的组件库（UI 组件 + 业务组件）
- ✅ 3 个参考页面模板
- ✅ 详细的 AI 开发规范
- ✅ 可以立即投入使用

所有新项目都可以从这个模板创建，确保前端代码的一致性和质量！
