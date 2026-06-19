# ERP 模板实现进度

## ✅ 已完成

### 1. 基础布局组件
- [x] Header.vue - 顶部导航栏
  - Logo + 公司名称（左）
  - 部门名称（中）
  - 主题切换 + 用户菜单（右）

- [x] RightPanel.vue - 右侧悬浮框
  - 模块切换（报价单/订单）
  - Tab 状态筛选（全部/待处理/进行中/已完成）
  - 搜索框
  - 新建按钮
  - 通知列表（未读显示）

- [x] MainLayout.vue - 主布局容器
  - 整合 Header 和 RightPanel
  - 左侧卡片列表区域
  - 面包屑导航
  - 模式切换（列表/通知详情/选择）

### 2. 业务组件
- [x] OrderCard.vue - 订单卡片
  - 标题区域（深色背景）
  - 编号 + 复制图标
  - 状态徽章
  - 内容区域（客户、日期、进度、金额）
  - 操作按钮区域

- [x] NotificationCard.vue - 通知卡片
  - 未读标记
  - 类型图标
  - 标题 + 描述 + 时间
  - 点击即已读

### 3. 页面更新
- [x] HomeView.vue - 使用 MainLayout
- [x] CLAUDE.md - 更新组件规范

### 4. 目录结构
```
src/
├── components/
│   ├── ui/                    # shadcn 组件
│   ├── business/              # 业务组件
│   │   ├── OrderCard.vue
│   │   ├── NotificationCard.vue
│   │   ├── QuotationCard.vue      # P1 - 待验收
│   │   ├── OrderEditForm.vue      # P1 - 待验收
│   │   ├── CustomerSelect.vue     # P1 - 待验收
│   │   ├── DashboardCharts.vue    # P2 - 待验收
│   │   ├── FileUpload.vue         # P2 - 待验收
│   │   ├── PrintTemplate.vue      # P2 - 待验收
│   │   ├── StatisticsCard.vue     # P2 - 待验收
│   │   ├── DataTable.vue          # P3 - 待验收
│   │   ├── Modal.vue              # P3 - 待验收
│   │   ├── Toast.vue              # P3 - 待验收
│   │   ├── Timeline.vue           # P3 - 待验收
│   │   ├── Pagination.vue         # P3 - 待验收
│   │   ├── CustomerDetail.vue     # P3 - 待验收
│   │   ├── ProductList.vue        # P3 - 待验收
│   │   ├── ProgressBar.vue        # P3 - 待验收
│   │   ├── EmptyState.vue         # P3 - 待验收
│   │   └── Loading.vue            # P3 - 待验收
│   └── layout/                # 布局组件
│       ├── Header.vue
│       ├── RightPanel.vue
│       └── MainLayout.vue
├── stores/                    # Pinia 状态管理（待实现）
└── composables/               # 组合式函数（待实现）
```

## 🚧 进行中

### 5. 待实现组件
- [ ] DualItemCard.vue - 阴阳项目卡片
- [ ] StatusBadge.vue - 状态徽章
- [ ] Drawer.vue - 抽屉组件
- [ ] SelectionMode.vue - 选择模式组件
- [ ] Breadcrumb.vue - 面包屑组件

### 6. 待实现功能
- [ ] 订单详情抽屉（左右分栏）
- [ ] 通知详情模式
- [ ] 选择模式（多选/拖拽）
- [ ] 联合开票流程
- [ ] 财务开票流程

## 📋 下一步

### 优先级 P0
1. 实现 Drawer 组件（订单详情抽屉）
2. 实现 DualItemCard 组件（阴阳项目）
3. 实现 StatusBadge 组件

### 优先级 P1
4. 实现选择模式
5. 实现通知详情模式
6. 实现联合开票流程

### 优先级 P2
7. 实现财务开票流程
8. 实现审批流程组件
9. 实现图表组件

##  当前状态

访问 http://localhost:5173/ 可以看到：
- 顶部导航栏
- 右侧悬浮框（模块切换、Tab 筛选、搜索、通知）
- 左侧订单卡片列表
- 点击通知卡片切换到通知详情模式

## 📝 注意事项

1. **复制功能**：OrderCard 中的复制图标已实现，点击复制编号到剪贴板
2. **通知逻辑**：点击通知卡片 → 左侧切换为通知详情模式
3. **响应式**：卡片列表支持响应式布局（1/2/3 列）
4. **主题支持**：支持亮色/暗色主题切换

## 🔧 技术细节

- 使用 Vue 3 Composition API
- TypeScript 类型安全
- Tailwind CSS 样式
- shadcn-vue 设计系统
- @lucide/vue 图标库
