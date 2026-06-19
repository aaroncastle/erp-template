## UI 设计系统

本项目基于公司标准模板创建，内置完整的设计系统。

### 设计系统架构

1. **设计令牌** (`src/assets/main.css`)
   - 颜色：基于 shadcn 的 CSS 变量（oklch 色彩空间）
   - 主色：绿色系（primary: oklch(0.841 0.238 128.85)）
   - 圆角：--radius: 0.625rem
   - 支持亮色/暗色主题

2. **UI 组件** (`src/components/ui/`)
   - 来自 shadcn-vue，禁止修改
   - Button：按钮组件（default/destructive/outline/secondary/ghost/link）
   - Input：输入框组件（bg-muted 背景色）
   - Card：卡片组件（Card/CardHeader/CardTitle/CardContent）
   - Table：表格组件（Table/TableHeader/TableBody/TableRow/TableHead/TableCell）
   - Label：表单标签组件
   - Checkbox：复选框组件

3. **业务组件** (`src/components/business/`)
   - OrderCard：订单卡片组件（含编号复制、状态徽章、操作按钮）
   - NotificationCard：通知卡片组件（点击即已读）
   - DualItemCard：阴阳项目卡片组件（待实现）
   - StatusBadge：状态徽章组件（待实现）

4. **布局组件** (`src/components/layout/`)
   - Header：顶部导航栏（Logo + 部门 + 主题 + 用户）
   - RightPanel：右侧悬浮框（模块切换 + Tab 筛选 + 搜索 + 通知）
   - MainLayout：主布局容器

5. **参考页面** (`src/pages/reference/`)
   - list-page.example.vue：列表页标准
   - form-page.example.vue：表单页标准
   - detail-page.example.vue：详情页标准

### 开发规范

#### 必须遵守

1. **颜色使用**
   - 使用 shadcn 语义化颜色：bg-primary, text-foreground, border-border 等
   - 禁止硬编码 hex/rgb/oklch 颜色值
   - 禁止使用 Tailwind 默认颜色（如 bg-blue-500）

2. **间距使用**
   - 使用标准 Tailwind 间距：p-4, m-2, gap-3 等
   - 禁止使用任意值（如 p-[13px]）

3. **圆角使用**
   - 使用 shadcn 圆角：rounded-sm, rounded-md, rounded-lg
   - 卡片统一使用 rounded-lg
   - 按钮统一使用 rounded-md

4. **组件使用**
   - 优先使用 shadcn-vue 组件
   - 业务组件使用封装的版本
   - 禁止重复实现已有组件

#### 布局规范

1. **主布局**
   - 顶部导航栏：Logo（左）+ 部门（中）+ 主题/用户（右）
   - 左侧主区域：卡片列表（可滚动）
   - 右侧悬浮框：模块切换 + Tab 筛选 + 搜索 + 通知列表

2. **卡片样式**
   - 标题区域（深色背景）+ 内容区域（浅色背景）
   - 编号后必须有复制图标
   - 状态使用徽章展示
   - 操作按钮放在卡片底部

3. **抽屉详情**
   - 右侧滑出，左右分栏
   - 左侧 60%：订单项列表（可滚动）
   - 右侧 40%：详情信息 + 操作栏（固定）

4. **通知中心**
   - 右侧悬浮框显示未读通知
   - 点击通知 → 立即已读 → 从列表移除
   - 左侧切换为通知关联的业务数据

#### 页面模板引用

- 列表页 → 参考 `src/pages/reference/list-page.example.vue`
- 表单页 → 参考 `src/pages/reference/form-page.example.vue`
- 详情页 → 参考 `src/pages/reference/detail-page.example.vue`

### 检查清单

每个页面完成后，必须验证：

- [ ] 使用了 shadcn 语义化颜色？
- [ ] 使用了标准 Tailwind 间距？
- [ ] 使用了封装的组件？
- [ ] 参考了对应的 example 页面？
- [ ] 响应式布局正确？
- [ ] 加载状态有骨架屏？
- [ ] 空状态有提示？
- [ ] 错误状态有反馈？
- [ ] 编号有复制图标？
- [ ] 操作栏在右侧（抽屉中）？

### 禁止事项

- ❌ 禁止修改 `src/components/ui/` 中的 shadcn 组件
- ❌ 禁止硬编码颜色、间距等值
- ❌ 禁止重复实现已有组件
- ❌ 禁止自由发挥页面结构
- ❌ 禁止使用 Tailwind 默认颜色类
-  禁止使用侧边栏导航
- ❌ 禁止新开页面（使用抽屉）

### 技术栈

- **框架**: Vue 3.5 + TypeScript 5.7
- **构建工具**: Vite 6
- **UI 库**: shadcn-vue（基于 Radix Vue + Tailwind CSS）
- **路由**: Vue Router 4
- **状态管理**: Pinia 2
- **包管理器**: pnpm
- **图标**: @lucide/vue

### 项目结构

```
src/
├── components/
│   ├── ui/                    # shadcn 组件（禁止修改）
│   │   ├── button/
│   │   ├── input/
│   │   ├── card/
│   │   ├── table/
│   │   ├── label/
│   │   └── checkbox/
│   ├── business/              # 业务组件
│   │   ├── OrderCard.vue
│   │   ├── NotificationCard.vue
│   │   ├── DualItemCard.vue   # 待实现
│   │   └── StatusBadge.vue    # 待实现
│   ├── layout/                # 布局组件
│   │   ├── Header.vue
│   │   ├── RightPanel.vue
│   │   └── MainLayout.vue
│   ├── PageHeader.vue         # 业务组件（旧）
│   └── DataTable.vue          # 业务组件（旧）
├── pages/
│   └── reference/             # 参考页面
│       ├── list-page.example.vue
│       ├── form-page.example.vue
│       └── detail-page.example.vue
├── views/                     # 实际页面
├── router/                    # 路由配置
├── stores/                    # Pinia 状态管理
├── composables/               # 组合式函数
├── lib/
│   ── utils.ts               # 工具函数
├── assets/
│   └── main.css               # 全局样式（含设计令牌）
├── App.vue
└── main.ts
```

### 开发命令

```bash
# 开发
pnpm dev

# 构建
pnpm build

# 预览
pnpm preview

# 代码检查
pnpm lint

# 格式化
pnpm format

# 类型检查
pnpm type-check
```
