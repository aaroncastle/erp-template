## 设计令牌

颜色基于 shadcn CSS 变量（oklch 色彩空间），支持亮色/暗色主题。

```css
/* 主色：绿色系 */
--primary: oklch(0.841 0.238 128.85);
--radius: 0.625rem;
```

完整令牌见 `src/assets/main.css`。

## 布局模式

### 卡片列表

```
┌─────────────────────────────┐
│ 标题区（bg-muted/30）        │  ← 编号 + 复制按钮 + 状态徽章
├─────────────────────────────┤
│ 内容区                       │  ← 关键信息 2-4 行
├─────────────────────────────┤
│ 操作区（bg-muted/20）        │  ← 操作按钮
─────────────────────────────┘
```

### 详情抽屉

```
┌────────────────────────┐
│ 列表区 60%    │ 详情 40%  │
│ （可滚动）    │ （固定）   │
│              │          │
│              │ 操作按钮   │
└──────────────┴──────────┘
```

### 表单页

Card 包裹，Label + Input 组合，按钮区底部右对齐。

## 开发约束

### 必须遵守

1. **颜色**：用 shadcn 语义化颜色（bg-primary, text-foreground, border-border）。禁止硬编码 hex/rgb/oklch，禁止 Tailwind 默认颜色（bg-blue-500）
2. **间距**：用标准 Tailwind 间距（p-4, m-2, gap-3）。禁止任意值（p-[13px]）
3. **圆角**：卡片 rounded-lg，按钮 rounded-md
4. **编号**：编号字段旁必须有复制按钮
5. **状态**：用 inline badge 展示

### 禁止事项

-  修改 `src/components/ui/` 中的 shadcn 组件
- ❌ 硬编码颜色、间距
- ❌ 使用 Tailwind 默认颜色类
- ❌ 使用侧边栏导航
- ❌ 新开页面（使用抽屉）
- ❌ 提前创建业务组件（复用 3 次以上才提取）

## 组件体系

### 第一层：shadcn UI（`src/components/ui/`，禁止修改）

Button、Input、Card、Table、Label、Checkbox

### 第二层：基础组件（`src/components/business/`，被其他组件依赖）

Avatar、Badge、Breadcrumb、DataTable、Drawer、DropdownMenu、EmptyState、Pagination、StatusBadge、UserSelect

### 第三层：样式参考组件

详见 `COMPONENT_REFERENCE.md`。开发时参考布局模式，**不要直接复用 props 接口**。

### 第四层：按需创建

直接用 shadcn 组合构建。复用 3 次以上才提取为独立组件。
