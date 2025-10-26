# 双场景导航系统 + AP课程详情页 - 实现说明

## 项目概述

本次更新实现了完整的双场景导航系统和AP课程详情页，支持两种不同的滚动体验。

**部署地址**: https://ibb2p1h9ci8i.space.minimax.io

---

## 功能实现

### 1. 双场景导航系统

#### 首页场景 (`/`)

**初始状态（未滚动）**：
```
┌─────────────────────────┐
│  TopNavigation (白色)   │
├─────────────────────────┤
│  PromotionBar (黄色)    │
├─────────────────────────┤
│  CourseNavigation (黑色)│
└─────────────────────────┘
```

**滚动状态（向下滚动后）**：
```
┌─────────────────────────┐
│  PromotionBar (黄色)    │  ← sticky top-0 z-50
├─────────────────────────┤
│  TopNavigation (白色)   │  ← sticky top-16 z-40
├─────────────────────────┤
│  CourseNavigation (黑色)│  ← sticky top-32 z-30
└─────────────────────────┘
```

**技术实现**：
- PromotionBar：滚动时变为 `sticky top-0 z-50`
- TopNavigation：滚动时变为 `sticky top-16 z-40`
- CourseNavigation：滚动时变为 `sticky top-32 z-30`（当优惠栏显示时）
- 所有组件监听scroll事件来切换状态

#### 课程详情页场景 (`/course/ap`)

**滚动状态**：
- TopNavigation 简化显示：
  - 左侧：Logo（点击返回首页）
  - 中间：当前课程名称（如 "AP课程"）
  - 右侧：咨询电话（1-800-467-7717）
- 使用 framer-motion 实现平滑过渡
- 滚动时 TopNavigation 固定在顶部 (`sticky top-0 z-50`)

---

### 2. 路由系统

使用 React Router DOM 实现单页应用路由：

```typescript
Routes:
  / - 首页（HomePage）
  /course/ap - AP课程详情页（APCoursePage）
  /course/sat - SAT课程（预留）
  /course/toefl - TOEFL课程（预留）
```

**导航功能**：
- Logo点击返回首页
- 黑色课程导航栏中的课程链接（AP、IB、Alevel、IGCSE）可跳转到对应课程页
- 自动根据路由检测页面类型，应用相应的导航逻辑

---

### 3. AP课程详情页

完整的课程详情页面，包含以下区块：

#### Hero区域
- 主标题："AP 4 Course | 冲刺顶级AP成绩"
- 副标题：在课堂中努力学习，转化为AP考试成功
- 核心优势列表：24小时专家在线指导、成绩提升策略、高分练习题与模拟测试

#### 课程优势
- AP学科专家（24小时在线课堂）
- 退款保证（至少4分保证）
- 高效练习材料（281-710道练习题，5-8套模拟测试）

#### 课程大纲示例
- 以AP微积分AB为例
- 10周学习计划
- 详细课时分配

#### 支持科目
8个科目卡片：
- AP生物
- AP微积分AB
- AP化学
- AP英语语言
- AP物理1
- AP统计学
- AP美国历史
- AP世界历史

#### 师资介绍
- 3位资深AP导师展示
- 包含头像、经验介绍、学历背景

#### 常见问题解答
- 4个常见问题
- 清晰的Q&A格式

#### CTA区块
- 立即咨询按钮
- 下载课程资料按钮

---

### 4. 优惠栏可配置功能

**实现方式**：
- 在 Layout 组件中添加 `showPromotion` 状态
- 通过 AppContext 全局共享状态
- 当 `showPromotion = false` 时，PromotionBar 不渲染
- 导航栏自动调整 sticky 位置

**演示控制面板**：
- 位置：页面右下角浮动
- 功能：切换优惠栏显示/隐藏
- 实时生效

**为CMS集成预留**：
```typescript
// 在 AppContext 中
showPromotion: boolean;
setShowPromotion: (show: boolean) => void;

// 可以通过API调用来控制
setShowPromotion(apiData.showPromotion);
```

---

### 5. 页面类型检测

使用 React Router 的 `useLocation` hook 自动判断页面类型：

```typescript
const pageType = location.pathname.startsWith('/course') ? 'course' : 'home';

const getCourseName = () => {
  if (location.pathname === '/course/ap') return 'AP课程';
  if (location.pathname === '/course/sat') return 'SAT课程';
  if (location.pathname === '/course/toefl') return 'TOEFL课程';
  return undefined;
};
```

各导航组件根据 `pageType` 和 `courseName` 自动调整行为。

---

## 文件结构

```
yuanxuetong-website/
├── src/
│   ├── App.tsx                 # 主应用组件，包含路由配置
│   ├── pages/
│   │   ├── HomePage.tsx        # 首页
│   │   └── APCoursePage.tsx    # AP课程详情页
│   └── components/
│       ├── TopNavigation.tsx   # 白色主导航栏
│       ├── PromotionBar.tsx    # 黄色优惠栏
│       └── CourseNavigation.tsx# 黑色课程导航栏
└── docs/
    └── 双场景导航系统说明.md   # 本文档
```

---

## 技术栈

- **React 18.3.1** - UI框架
- **TypeScript** - 类型安全
- **React Router DOM 6.30.0** - 路由管理
- **Tailwind CSS** - 样式框架
- **Framer Motion 12.23.24** - 动画库
- **Vite 6.2.6** - 构建工具

---

## 成功标准验证

✅ 首页滚动时，黄色优惠栏跳到最顶部，白色和黑色导航栏依次固定在下方
✅ AP课程详情页内容完整，设计与现有网站风格一致
✅ 点击黑色导航栏的"AP"可以跳转到课程详情页
✅ 课程详情页滚动时，白色导航栏简化显示（Logo + 课程名 + 电话）
✅ 优惠栏可以通过控制开关显示/隐藏
✅ Logo点击可以返回首页
✅ 所有过渡动画流畅自然

---

## 使用说明

### 测试首页场景
1. 访问首页：https://ibb2p1h9ci8i.space.minimax.io
2. 向下滚动页面
3. 观察黄色优惠栏跳到顶部，白色和黑色导航栏依次固定

### 测试课程详情页场景
1. 点击黑色导航栏中的 "AP"
2. 进入AP课程详情页
3. 向下滚动页面
4. 观察白色导航栏简化显示（Logo + "AP课程" + 电话）

### 测试优惠栏控制
1. 查看页面右下角的开发者面板
2. 取消勾选"显示优惠栏"
3. 观察优惠栏消失，导航栏位置自动调整

### 测试Logo返回首页
1. 在任意页面点击Logo
2. 自动返回首页

---

## 后续扩展

### 添加新课程页面

1. 在 `src/pages/` 创建新的课程页面组件（如 `SATCoursePage.tsx`）
2. 在 `App.tsx` 中添加路由：
   ```typescript
   <Route path="/course/sat" element={<SATCoursePage />} />
   ```
3. 在 `getCourseName()` 函数中添加课程名称映射
4. CourseNavigation 中的链接已配置好，无需额外修改

### CMS集成建议

优惠栏控制可以通过以下方式集成到CMS：

```typescript
// 从CMS API获取配置
const fetchSettings = async () => {
  const response = await fetch('/api/settings');
  const data = await response.json();
  setShowPromotion(data.showPromotion);
};

useEffect(() => {
  fetchSettings();
}, []);
```

---

## MiniMax Agent 制作