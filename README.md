# 前端设计系统模板

通用前端设计系统，适用于任何 Vue 3 + shadcn-vue 项目。

## 集成到新项目

### 方式一：直接复制（推荐）

```bash
# 将 src/components/ 复制到你的项目
cp -r src/components/ /your-project/src/components/
cp src/assets/main.css /your-project/src/assets/main.css
cp CLAUDE.md /your-project/
cp DESIGN_SYSTEM.md /your-project/
cp COMPONENT_REFERENCE.md /your-project/
```

然后在你的项目 CLAUDE.md 中添加引用：

```markdown
## 前端设计系统

参考 `DESIGN_SYSTEM.md` 获取设计令牌和布局规范。
参考 `COMPONENT_REFERENCE.md` 获取组件用途说明。
```

### 方式二：Monorepo

```yaml
# pnpm-workspace.yaml
packages:
  - 'packages/web'    # 放入此模板
  - 'packages/server' # 你的后端
```

## 高效开发的三个前提

AI 生成高质量前端代码需要以下三项。第一项由本模板提供，后两项需要你在项目中准备：

| 前提 | 作用 | 来源 | 如何获得 |
|------|------|------|----------|
| **设计系统** | 知道"怎么画" | ✅ 本模板 | 复制 `CLAUDE.md` + `DESIGN_SYSTEM.md` 到项目 |
| **布局规划** | 知道"画在哪里" | 你的项目 | 与 AI 讨论：导航模式、页面层级、详情展示方式，固定后记录在项目文档中 |
| **数据模型** | 知道"画什么" | 你的项目 | 从业务需求出发，与 AI 讨论梳理字段和关系；**粗略即可，开发中迭代完善** |

> **提示**：后两项不需要完美。有一个大致的方向就能开始，AI 在生成组件时会帮你发现缺失的字段或关系，这时再补充调整。这是正常的迭代过程。

## 使用原则

- 用 shadcn 基础组件直接组合页面，不要从模板中找业务组件来适配数据
- 模板中的样式参考组件只看**布局模式**，不复用其 props 接口
- 同一个 UI 模式在 **3 个以上页面**复用时，才提取为独立组件

## 这个模板提供什么

```
src/components/
├── ui/              ← shadcn 基础组件（禁止修改）
├── business/        ← 基础组件 10 个 + 样式参考组件 19 个
└── layout/          ← 布局组件（Header, RightPanel, MainLayout）
```

- **基础组件**：被其他组件依赖的通用 UI（DataTable、Drawer、StatusBadge 等）
- **样式参考组件**：展示如何将 shadcn 组合为业务 UI，开发时参考布局模式，不要直接复用 props
- **参考页面**：`src/pages/reference/` 下的 list/form/detail 示例

## 设计约束

- 颜色：用 shadcn 语义化颜色（bg-primary, text-foreground），禁止硬编码 hex 或 Tailwind 默认颜色
- 间距：用标准 Tailwind 间距（p-4, gap-3），禁止任意值
- 圆角：卡片 rounded-lg，按钮 rounded-md
- 编号字段旁必须有复制按钮
- 详情用右侧抽屉（60/40 分栏），禁止新开页面

完整规范见 `DESIGN_SYSTEM.md`。

## 开发命令

```bash
pnpm dev          # 开发
pnpm build        # 构建
pnpm preview      # 预览（访问 /preview 路由）
pnpm lint         # 代码检查
pnpm format       # 格式化
pnpm type-check   # 类型检查
```

## 技术栈

Vue 3.5 / TypeScript 5.7 / Vite 6 / shadcn-vue / Tailwind CSS / Pinia 2 / @lucide/vue
