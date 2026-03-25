# React 编写代码规范（一定要遵守）

## 目录
1. 组件与目录架构规范 (Directory Architecture)
2. 逻辑与视图解耦：自定义 Hook (Custom Hooks)
3. 命名与事件绑定规范 (Naming Conventions)
4. 安全的条件渲染 (Conditional Rendering)
5. 状态下放 (State Colocation)
6. 内容提升 (Component Composition)
7. Context 细粒度拆分
8. 避免在 useEffect 中同步更新状态 (Derived State)
9. 缓存钩子规范 (useMemo / useCallback / React.memo)
10. 列表 Key 规范
11. 长列表虚拟化 (Virtualization)
12. 路由与组件级代码分割 (Code Splitting)
13. 状态过渡 (useTransition)
14. 巨型组件拆分规范 (Component Decomposition)

---

## 组件与目录架构规范 (Directory Architecture)
 
严禁将所有的组件、逻辑、样式全部堆砌在一个文件夹（如 `src/components`）中。随着项目变大，必须按照“业务功能（Feature）”或“职责”进行模块化拆分。

* **规范**：推荐使用基于功能的目录划分（Feature-based Structure）。
    * `src/components/`：仅存放全局通用的纯 UI 组件（如 Button, Modal, Input）。
    * `src/features/`：按业务模块划分（如 auth/, dashboard/, profile/），每个业务模块内部再包含自己的 components/, hooks/, api/。
    * `src/pages/` 或 `src/views/`：仅作为路由的入口文件，负责组装 Feature 模块，不写具体 UI 细节。
    * `src/hooks/`：存放全局通用的自定义 Hook。
    * `src/utils/`：存放与 React 无关的纯函数工具（如日期格式化）。

## 逻辑与视图解耦：自定义 Hook (Custom Hooks)

严禁在 UI 组件（大写字母开头的函数）中堆砌几十上百行的 `useState`、`useEffect` 和网络请求逻辑。

* **规范**：UI 组件应当尽可能保持“纯粹”，只负责“接收数据并渲染视图”。复杂的业务逻辑、状态管理和副作用必须抽离到自定义 Hook 中（以 `use` 开头）。

**Good:**

```tsx
// 1. 将逻辑抽离到单独的 Hook 文件中 (useUserData.ts)
function useUserData(userId: string) {
  const [data, setData] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    fetchUser(userId).then(res => {
      setData(res);
      setIsLoading(false);
    });
  }, [userId]);

  return { data, isLoading };
}

// 2. UI 组件变得极其清爽 (UserProfile.tsx)
function UserProfile({ userId }) {
  const { data, isLoading } = useUserData(userId);

  if (isLoading) return <Spinner />;
  return <div>{data.name}</div>;
}
```

## 命名与事件绑定规范 (Naming Conventions)

* **文件与组件命名**：React 组件文件和组件名必须使用大驼峰命名法（PascalCase），例如 `UserProfile.tsx`、`SidebarMenu.tsx`。
* **自定义 Hook 命名**：必须以小写 `use` 开头，遵循小驼峰命名法（camelCase），例如 `useAuth`、`useWindowSize`。
* **事件 Prop 命名**：传递给组件的事件属性名应当以 `on` 开头（如 `onSubmit`、`onChange`）。
* **内部处理函数命名**：组件内部处理事件的函数应当以 `handle` 开头（如 `handleSubmit`、`handleChange`）。

**Good:**

```tsx
function SearchForm({ onSearch }) { // Prop 以 on 开头
  const [query, setQuery] = useState('');

  // 内部函数以 handle 开头
  const handleSubmit = (e) => {
    e.preventDefault();
    onSearch(query);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <button type="submit">Search</button>
    </form>
  );
}
```

## 安全的条件渲染 (Conditional Rendering)

在 React 中使用逻辑与（`&&`）进行条件渲染时，如果是数字 `0`，React 会直接在页面上渲染出 `0`，这会导致极大的 UI 污染。

* **红线**：严禁直接将可能为 `0` 的数字类型变量作为 `&&` 的前置条件。
* **规范**：必须将其转换为布尔值（使用 `!!` 或 `> 0`），或者使用三元运算符 `? :`。

**Good:**

```tsx
function ShoppingCart({ items }) {
  return (
    <div>
      {/* 错误写法：如果 items.length 是 0，页面上会显示一个孤零零的 "0" */}
      {/* {items.length && <span>有商品</span>} */}

      {/* 规范写法 1：明确判断条件 */}
      {items.length > 0 && <span>有商品</span>}

      {/* 规范写法 2：强制转为布尔值 */}
      {!!items.length && <span>有商品</span>}

      {/* 规范写法 3：使用三元运算符处理两种分支 */}
      {items.length ? <span>有商品</span> : <span>空空如也</span>}
    </div>
  );
}
```

## 状态下放 (State Colocation)

将只影响局部视图的状态移动到真正消费它的子组件中，避免触发父组件及其他无关子组件的渲染。

**Good:**

```tsx
// 将弹窗的开关状态下放到独立的 ModalWrapper 中，隔离渲染范围
function ModalWrapper() {
  const [isOpen, setIsOpen] = useState(false);
  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>
      {isOpen && <Modal />}
    </>
  );
}

function App() {
  return (
    <div>
      <ModalWrapper />
      <ExpensiveComponent /> {/* App 不重新渲染，此组件始终安全 */}
    </div>
  );
}
```

## 内容提升 (Component Composition / children 模式)

当父组件必须持有高频更新的状态时，通过 `props.children` 将昂贵的子组件从外部传入。React 不会重新渲染通过 props 传入的 React Elements。

**Good:**

```tsx
function ScrollTracker({ children }) {
  const [scrollY, setScrollY] = useState(0);
  // 监听滚动的逻辑...
  return (
    <div onScroll={handleScroll}>
      <p>Scrolled: {scrollY}px</p>
      {children} {/* 即使 scrollY 疯狂更新，children 也不会 Re-render */}
    </div>
  );
}

// 使用
<ScrollTracker>
  <VeryExpensiveChart /> 
</ScrollTracker>
```

## Context 细粒度拆分

**严禁**将所有全局状态塞入单一 Context。Context 的更新会无视 `React.memo` 强制向下渲染。

* **规范**：按业务域拆分（如 AuthContext, ThemeContext）。对于高频且复杂的全局状态，必须使用 Zustand 或 Redux 等支持 Selector 细粒度订阅的外部库。

## 避免在 useEffect 中同步更新状态 (Derived State)

**严禁**在 `useEffect` 中同步调用 `setState` 去响应 Props 或其他 State 的变化（如 `eslint(react-hooks/set-state-in-effect)` 警告）。这会导致“级联渲染 (Cascading Renders)”——React 渲染完第一遍后，执行 Effect 再次触发状态更新，被迫立即进行第二次全量渲染，严重损耗性能。

* **规范**：如果一个值可以基于现有的 Props 或 State 计算得出，不要把它作为一个独立的 State 去维护，而是直接在渲染期间计算（即派生状态）。如果确实需要缓存，使用 `useMemo`。如果在 Props 改变时需要重置子组件内部的完整状态，请优先使用更改组件 `key` 属性的方式。

**Good:**

```tsx
// 直接在渲染期间计算派生状态，彻底抛弃多余的 useState 和 useEffect
function ThemeConfigurator({ activeTarget, colorHex }) {
  
  // 规范 1：直接派生状态 (Derived State)。每次渲染直接获取最新值。
  const colorValue = colorHex; 

  // 规范 2：如果由 colorHex 转换出新颜色的计算过程非常耗时，才使用 useMemo 缓存
  const complexColorValue = useMemo(() => {
    return expensiveColorTransform(colorHex);
  }, [colorHex]);

  return <div style={{ color: colorValue }}>Target: {activeTarget}</div>;
}
```

## 缓存钩子规范 (useMemo / useCallback / React.memo)

* **适用场景**：仅当向 `React.memo` 包裹的子组件传递引用类型（对象、数组、函数），或进行极大开销的数学计算时使用。
* **反模式**：滥用 `useCallback` 包装所有简单函数，缓存本身的闭包开销可能大于创建新函数的开销。

**Good:**

```tsx
const MemoizedChild = React.memo(Child);

function Parent({ data }) {
  // 仅在 data 改变时重新计算，维持引用地址稳定
  const processedData = useMemo(() => expensiveSort(data), [data]);
  
  // 维持函数引用稳定，防止 MemoizedChild 无效渲染
  const handleClick = useCallback(() => {
    console.log("Clicked");
  }, []);

  return <MemoizedChild data={processedData} onClick={handleClick} />;
}
```

## 列表 Key 规范

* **红线**：绝对禁止使用 `index` 或 `Math.random()` 作为动态列表的 key。
* **规范**：必须使用数据的唯一且稳定的标识（如数据库 ID `item.id`）。

## 长列表虚拟化 (Virtualization)

当一次性渲染超过 100 个 DOM 节点时，禁止全量渲染。

* **规范**：必须引入 `react-window` 或 `react-virtualized`，仅渲染可视区域内的 DOM 节点。

## 路由与组件级代码分割 (Code Splitting)

对于非首屏关键路径的大型组件或页面，必须使用 `React.lazy` 进行按需加载，减小主 Bundle 体积。

**Good:**

```tsx
import { lazy, Suspense } from 'react';

// 只有在渲染时才会去下载这个组件的 JS 代码
const HeavyChart = lazy(() => import('./HeavyChart'));

function Dashboard() {
  return (
    <div>
      <h1>Metrics</h1>
      <Suspense fallback={<Spinner />}>
        <HeavyChart />
      </Suspense>
    </div>
  );
}
```

## 状态过渡 (useTransition)

对于会导致页面大规模重排或卡顿的状态更新（如切换复杂视图），将其标记为低优先级过渡任务，确保 UI 线程（如输入框响应、按钮点击）不被阻塞。

**Good:**

```tsx
import { useState, useTransition } from 'react';

function SearchPage() {
  const [isPending, startTransition] = useTransition();
  const [keyword, setKeyword] = useState("");
  const [query, setQuery] = useState("");

  const handleChange = (e) => {
    setKeyword(e.target.value); // 高优先级：输入框立即可见
    startTransition(() => {
      setQuery(e.target.value); // 低优先级：下方耗时的列表过滤可以慢慢来
    });
  };

  return (
    <div>
      <input value={keyword} onChange={handleChange} />
      {isPending && <Spinner />}
      <SlowList query={query} />
    </div>
  );
}
```

## 巨型组件拆分规范 (Component Decomposition)

严禁写出动辄几百上千行的“巨型组件” (Monster Component)，也严禁为了省事将多个本该独立的子组件全部堆砌在同一个单一文件（脚本）中。这会导致代码可读性极差、极易引发冲突且难以维护。

红线：单个组件文件代码尽量不超过 300 行。

规范：必须遵循“单一职责原则”对复杂 UI 进行物理拆分。将 Header、Sidebar、Table、Form、Modal 等独立区块抽离成单独的 .tsx 文件。如果是仅属于当前业务线的专属子组件，应当在此业务目录下新建一个 components/ 文件夹进行就近管理。主组件（父组件）应该像一个“组装车间”，只负责引入拼装和调度，坚决不写所有具体的 UI 细节。

**Good:**

```tsx
// 错误示范：将头部、侧边栏、图表全写在同一个 Dashboard.tsx 里。
// 正确示范：将各个区块拆分为独立文件，主文件只负责简洁组装。
import { DashboardHeader } from './components/DashboardHeader';
import { UserStatistics } from './components/UserStatistics';
import { RecentActivity } from './components/RecentActivity';

export default function Dashboard() {
  const { data, isLoading } = useDashboardData(); // 逻辑已抽离

  if (isLoading) return <Spinner />;

  return (
    <div className="dashboard-layout">
      {/* 仅仅在此处调用，细节隐藏在子组件内部 */}
      <DashboardHeader user={data.user} />
      <main>
        <UserStatistics stats={data.stats} />
        <RecentActivity activities={data.activities} />
      </main>
    </div>
  );
}
```