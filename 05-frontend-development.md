# 前端开发教程

深入学习 Project N.E.K.O. 项目的前端开发，包括基于 React 19 + Vite 7 的现代化前端架构和传统 HTML/JS 前端。

## 📋 目录

- [前端架构概览](#前端架构概览)
- [React 前端开发](#react-前端开发)
  - [技术栈](#技术栈)
  - [项目结构](#项目结构)
  - [开发环境设置](#开发环境设置)
  - [创建新组件](#创建新组件)
  - [修改主应用](#修改主应用)
- [传统前端开发](#传统前端开发)
- [组件开发指南](#组件开发指南)
- [API 集成](#api-集成)
- [桥接层使用](#桥接层使用)
- [样式和 UI](#样式和-ui)
- [测试](#测试)
- [构建和部署](#构建和部署)
- [调试技巧](#调试技巧)

---

## 前端架构概览

Project N.E.K.O. 项目采用**混合架构**：

1. **React SPA 应用**（新架构）
   - 位于 `frontend/` 目录
   - 使用 React 19 + Vite 7 + TypeScript
   - 采用 npm workspaces 管理多个子包
   - 构建产物输出到 `static/bundles/`

2. **传统 HTML/JS 前端**（旧架构）
   - 位于 `templates/` 和 `static/` 目录
   - 逐步迁移到 React

> 💡 **提示**：关于项目结构，请参考 [项目架构与目录结构 - 前端模块](./03-project-structure.md#前端模块)。

---

## React 前端开发

### 技术栈

- **框架**：React 19.1.1
- **构建工具**：Vite 7.1.7
- **语言**：TypeScript 5.9.2
- **HTTP 客户端**：Axios 1.13.2
- **测试框架**：Vitest 2.1.3（含 coverage-v8）
- **包管理**：npm workspaces

### 项目结构

```
frontend/
├── src/
│   ├── web/              # SPA 应用入口
│   │   ├── main.tsx      # React 挂载点
│   │   ├── App.tsx       # 主应用组件
│   │   └── styles.css    # 全局样式
│   └── types/            # TypeScript 类型定义
├── packages/             # npm workspaces 子包
│   ├── components/       # UI 组件库
│   │   ├── src/
│   │   │   ├── Button.tsx
│   │   │   ├── Button.css
│   │   │   ├── StatusToast.tsx
│   │   │   ├── StatusToast.css
│   │   │   └── Modal/          # 模态框组件
│   │   │       ├── BaseModal.tsx
│   │   │       ├── AlertDialog.tsx
│   │   │       ├── ConfirmDialog.tsx
│   │   │       ├── PromptDialog.tsx
│   │   │       └── index.tsx
│   │   └── index.ts      # 组件导出入口
│   ├── request/          # HTTP 请求库（Axios 封装）
│   │   ├── __tests__/    # 单元测试
│   │   │   ├── requestClient.test.ts
│   │   │   ├── entrypoints.test.ts
│   │   │   └── nativeStorage.test.ts
│   │   ├── coverage/     # 测试覆盖率报告
│   │   ├── createClient.ts
│   │   ├── index.ts      # 通用入口
│   │   ├── index.web.ts  # Web 端入口（默认实例）
│   │   ├── index.native.ts # React Native 入口
│   │   └── src/
│   │       ├── request-client/  # 请求客户端核心
│   │       │   ├── requestQueue.ts  # 请求队列管理
│   │       │   ├── tokenStorage.ts  # Token 存储实现
│   │       │   └── types.ts         # 类型定义
│   │       └── storage/          # 存储抽象层
│   │           ├── webStorage.ts     # localStorage 封装
│   │           ├── nativeStorage.ts  # AsyncStorage 封装
│   │           └── types.ts          # Storage 接口
│   ├── web-bridge/       # 桥接层（将组件与请求能力暴露到 window）
│   │   ├── src/
│   │   │   ├── index.ts  # 桥接函数
│   │   │   └── global.ts # 全局类型声明
│   │   └── vite.config.ts
│   └── common/           # 公共工具与类型
│       └── index.ts
├── scripts/              # 构建辅助脚本
├── vendor/               # 第三方库源文件
│   └── react/            # React/ReactDOM UMD 文件
├── index.html            # 开发环境 HTML 模板
├── vite.web.config.ts    # Web 应用 Vite 配置
├── tsconfig.json         # TypeScript 配置
└── package.json          # 工作区根配置
```

### 环境要求

- **Node.js**：推荐 22.x 或 20.x LTS；最低 18.x
- **npm**：推荐 11.x；最低 10.x

### 开发环境设置

```bash
# 进入前端目录
cd frontend

# 安装依赖（会自动安装所有 workspaces 的依赖）
npm install

# 启动 Web 应用开发服务器
npm run dev:web
# 默认访问地址: http://localhost:5173

# 启动 Common 包调试（可选）
npm run dev:common
```

### 路径别名

项目配置了以下路径别名，可在代码中直接使用：

- `@project_neko/components` → `packages/components/index.ts`
- `@project_neko/common` → `packages/common/index.ts`
- `@project_neko/request` → `packages/request/index.ts`

这些别名在 `tsconfig.json` 和 `vite.web.config.ts` 中均有配置。

### 创建新组件

在 `packages/components/src/` 目录下创建组件：

1. **创建组件文件**

```typescript
// packages/components/src/MyComponent.tsx
import "./MyComponent.css";

type MyComponentProps = {
  title: string;
  onClick?: () => void;
};

export function MyComponent({ title, onClick }: MyComponentProps) {
  return (
    <div className="my-component">
      <h1>{title}</h1>
      {onClick && <button onClick={onClick}>点击</button>}
    </div>
  );
}
```

2. **创建样式文件**

```css
/* packages/components/src/MyComponent.css */
.my-component {
  padding: 20px;
  background: #f0f0f0;
}
```

3. **导出组件**

在 `packages/components/index.ts` 中添加：

```typescript
export { MyComponent } from "./src/MyComponent";
```

4. **使用组件**

在 `src/web/App.tsx` 或其他组件中使用：

```typescript
import { MyComponent } from "@project_neko/components";

function App() {
  return (
    <div>
      <MyComponent title="我的组件" onClick={() => console.log("点击")} />
    </div>
  );
}
```

### 修改主应用

编辑 `src/web/App.tsx` 来修改主应用：

```typescript
import "./styles.css";
import { Button } from "@project_neko/components";
import { createRequestClient, WebTokenStorage } from "@project_neko/request";

// 创建请求客户端
const request = createRequestClient({
  baseURL: "http://localhost:48911",
  storage: new WebTokenStorage(),
  refreshApi: async () => {
    // 实现 Token 刷新逻辑
    throw new Error("refreshApi not implemented");
  },
  returnDataOnly: true
});

function App() {
  const handleClick = async () => {
    try {
      const data = await request.get("/api/config/page_config");
      console.log("数据:", data);
    } catch (err) {
      console.error("请求失败", err);
    }
  };

  return (
    <main className="app">
      <h1>N.E.K.O 前端</h1>
      <Button label="获取数据" onClick={handleClick} />
    </main>
  );
}

export default App;
```

---

## 传统前端开发

传统前端位于 `templates/` 和 `static/` 目录，主要用于 Live2D 展示和基础交互。

> 💡 **提示**：关于传统前端结构，请参考 [项目架构与目录结构 - HTML 模板](./03-project-structure.md#html-模板-templates) 和 [静态资源](./03-project-structure.md#静态资源-static)。

### 项目结构

```
templates/
├── index.html              # 主页面
├── api_key_settings.html   # API 密钥设置
├── chara_manager.html      # 角色管理
├── l2d_manager.html        # Live2D 管理
└── ...                     # 其他页面

static/
├── app.js                  # 主应用逻辑
├── common_ui.js            # UI 工具函数
├── common_dialogs.js       # 对话框工具
├── i18n-i18next.js         # 国际化
├── live2d-*.js             # Live2D 相关脚本
├── libs/                   # 第三方库
│   ├── live2d.min.js
│   ├── pixi.min.js
│   └── i18next.min.js
└── icons/                  # 图标资源
```

### 修改 HTML

编辑 `templates/index.html` 或其他模板文件：

```html
<div id="my-section">
  <h1>我的内容</h1>
  <button id="my-button">点击</button>
</div>

<script>
  // 在页面底部添加脚本
  document.getElementById('my-button').addEventListener('click', () => {
    console.log('按钮被点击');
  });
</script>
```

### 修改 JavaScript

编辑 `static/app.js` 或创建新文件：

```javascript
// 使用全局工具函数
window.showDialog('提示', '这是一个对话框');

// 使用国际化
const text = window.i18next.t('app.title');

// Live2D 相关操作
if (window.live2dModel) {
  window.live2dModel.setExpression('happy');
}
```

### 使用 React 组件（在传统前端中）

如果需要在传统前端中使用 React 组件，可以引用构建后的 UMD 格式：

```html
<!-- 在 HTML 模板中引入 -->
<script src="/static/bundles/react.production.min.js"></script>
<script src="/static/bundles/react-dom.production.min.js"></script>
<link rel="stylesheet" href="/static/bundles/components.css" />
<script src="/static/bundles/components.js"></script>

<script type="module">
  // 使用全局变量访问组件
  const { Button } = ProjectNekoComponents;
  const { createRoot } = ReactDOM;

  // 挂载组件
  const root = createRoot(document.getElementById('react-root'));
  root.render(React.createElement(Button, { 
    label: '点击我',
    onClick: () => alert('点击了按钮')
  }));
</script>
```

---

## 组件开发指南

### React 组件最佳实践

#### 1. 函数组件（推荐）

使用函数组件和 TypeScript：

```typescript
// packages/components/src/Button.tsx
import "./Button.css";

type ButtonProps = {
  label: string;
  onClick?: () => void;
  disabled?: boolean;
};

export function Button({ label, onClick, disabled = false }: ButtonProps) {
  return (
    <button 
      className="btn" 
      type="button" 
      onClick={onClick}
      disabled={disabled}
    >
      {label}
    </button>
  );
}
```

#### 2. 使用 Hooks

```typescript
import { useState, useEffect } from "react";

export function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `计数: ${count}`;
  }, [count]);

  return (
    <div>
      <p>计数: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        增加
      </button>
    </div>
  );
}
```

#### 3. 自定义 Hooks

在 `packages/common/` 中创建自定义 Hook：

```typescript
// packages/common/hooks/useApi.ts
import { useState, useEffect } from "react";
import { request } from "@project_neko/request";

export function useApi<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    setLoading(true);
    request
      .get<T>(url)
      .then((data) => {
        setData(data);
        setLoading(false);
      })
      .catch((err) => {
        setError(err);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}
```

在 `packages/common/index.ts` 中导出：

```typescript
export { useApi } from "./hooks/useApi";
```

使用：

```typescript
import { useApi } from "@project_neko/common";

function MyComponent() {
  const { data, loading, error } = useApi("/api/endpoint");

  if (loading) return <div>加载中...</div>;
  if (error) return <div>错误: {error.message}</div>;
  return <div>{JSON.stringify(data)}</div>;
}
```

#### 4. 组件导出

在 `packages/components/index.ts` 中统一导出：

```typescript
export { Button } from "./src/Button";
export { Counter } from "./src/Counter";
// ... 其他组件
```

---

## API 集成

### 使用 Request 模块

N.E.K.O 项目提供了基于 Axios 封装的请求库，支持：

- **请求队列**：自动管理并发请求，Token 刷新期间暂存新请求
- **Token 管理**：自动存储和刷新访问令牌，支持 401 自动刷新
- **平台适配**：支持 Web（localStorage）和 React Native（AsyncStorage）
- **错误处理**：统一的错误处理机制，支持自定义错误处理器
- **请求日志**：可配置的请求/响应日志，开发环境自动启用

#### 配置选项

`createRequestClient(options)` 支持以下配置：

| 选项 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `baseURL` | `string` | ✅ | - | API 基础 URL |
| `storage` | `TokenStorage` | ✅ | - | Token 存储实现 |
| `refreshApi` | `TokenRefreshFn` | ✅ | - | Token 刷新函数 |
| `timeout` | `number` | - | `15000` | 请求超时时间（毫秒） |
| `requestInterceptor` | `Function` | - | - | 自定义请求拦截器 |
| `responseInterceptor` | `Object` | - | - | 自定义响应拦截器 |
| `returnDataOnly` | `boolean` | - | `true` | 是否只返回 `response.data` |
| `errorHandler` | `Function` | - | - | 自定义错误处理器 |
| `logEnabled` | `boolean` | - | auto | 是否启用请求日志 |

#### 日志控制

请求日志的启用优先级：
1. `config.logEnabled`（配置项覆盖）
2. `globalThis.NEKO_REQUEST_LOG_ENABLED`（全局变量）
3. `import.meta.env.MODE`（构建模式，development 时启用）
4. 默认关闭

#### 在 React 组件中使用

**方法一：使用默认实例（推荐）**

```typescript
import { request } from "@project_neko/request";

function MyComponent() {
  const fetchData = async () => {
    try {
      // 默认实例已配置 baseURL: "/api" 和 Token 刷新
      const data = await request.get("/config/page_config", {
        params: { lanlan_name: "test" }
      });
      console.log("数据:", data);
    } catch (error) {
      console.error("请求失败:", error);
    }
  };

  return <button onClick={fetchData}>获取数据</button>;
}
```

**方法二：创建自定义实例**

```typescript
import { createRequestClient, WebTokenStorage } from "@project_neko/request";

// 创建自定义请求客户端
const customRequest = createRequestClient({
  baseURL: "http://localhost:48911",
  storage: new WebTokenStorage(),
  refreshApi: async (refreshToken: string) => {
    // 实现 Token 刷新逻辑
    const response = await fetch("/api/auth/refresh", {
      method: "POST",
      body: JSON.stringify({ refreshToken }),
      headers: { "Content-Type": "application/json" }
    });
    const data = await response.json();
    return {
      accessToken: data.access_token,
      refreshToken: data.refresh_token
    };
  },
  returnDataOnly: true,  // 只返回 data，不返回完整 response
  logEnabled: true       // 强制启用日志（可选）
});

// 使用自定义实例
const data = await customRequest.get("/api/endpoint");
```

#### 在传统 JS 中使用

传统前端可以直接使用构建后的 UMD 格式：

```html
<!-- 在 HTML 模板中引入 -->
<script src="/static/bundles/request.js"></script>
<script>
  // 使用全局变量
  const request = ProjectNekoRequest.createRequestClient({
    baseURL: "/api",
    storage: new ProjectNekoRequest.WebTokenStorage(),
    returnDataOnly: true
  });

  request.get("/api/endpoint")
    .then(data => {
      console.log("数据:", data);
    })
    .catch(error => {
      console.error("请求失败:", error);
    });
</script>
```

### 请求方法

请求客户端支持所有 Axios 方法：

```typescript
// GET 请求
const data = await request.get("/api/endpoint", {
  params: { key: "value" }
});

// POST 请求
const result = await request.post("/api/endpoint", {
  body: { key: "value" }
});

// PUT 请求
await request.put("/api/endpoint", { data: { key: "value" } });

// DELETE 请求
await request.delete("/api/endpoint");
```

---

## 桥接层使用

桥接层（`@project_neko/web-bridge`）将 React 组件和请求能力暴露到 `window` 对象，供非 React 代码使用。

### 主要功能

- **bindStatusToastToWindow()**：将 `StatusToast` 绑定到 `window.showStatusToast()`
- **bindModalToWindow()**：将 `Modal` 绑定到 `window.showAlert()`、`window.showConfirm()`、`window.showPrompt()`
- **bindRequestToWindow()**：将请求客户端绑定到 `window.request`，并提供 URL 构建工具
- **autoBindDefaultRequest()**：自动绑定默认请求客户端（UMD 加载时自动执行）

### 在 HTML 中使用桥接层

```html
<!-- 1. 引入请求库（不依赖 React，可先加载） -->
<script src="/static/bundles/request.js"></script>

<!-- 2. 引入桥接层（封装全局工具，自动绑定 window.request） -->
<script src="/static/bundles/web-bridge.js"></script>

<!-- 3. 引入 React/ReactDOM UMD（组件库依赖） -->
<script src="/static/bundles/react.production.min.js"></script>
<script src="/static/bundles/react-dom.production.min.js"></script>

<!-- 4. 引入组件库样式 -->
<link rel="stylesheet" href="/static/bundles/components.css">

<!-- 5. 引入组件库 UMD -->
<script src="/static/bundles/components.js"></script>
```

### 全局 API 使用

#### 状态提示

```javascript
window.showStatusToast(message, duration);
// 示例: window.showStatusToast("操作成功", 3000);
```

#### 模态对话框

```javascript
// Alert（警告框）
await window.showAlert(message, title);

// Confirm（确认框）
const ok = await window.showConfirm(message, title, {
  okText: "确定",
  cancelText: "取消",
  danger: false
});

// Prompt（输入框）
const value = await window.showPrompt(message, defaultValue, title);
```

#### HTTP 请求

```javascript
// GET 请求
const data = await window.request.get("/api/endpoint", { params: { key: value } });

// POST 请求
const result = await window.request.post("/api/endpoint", { body: data });

// URL 构建
const apiUrl = window.buildApiUrl("/api/endpoint");
const staticUrl = window.buildStaticUrl("/static/resource.png");
const wsUrl = window.buildWebSocketUrl("/ws/chat");
```

### 事件系统

```javascript
// 请求客户端就绪
window.addEventListener('requestReady', () => {
  console.log('window.request 已可用');
});

// 状态提示组件就绪
window.addEventListener('statusToastReady', () => {
  console.log('window.showStatusToast 已可用');
});

// Modal 组件就绪
window.addEventListener('modalReady', () => {
  console.log('window.showAlert/showConfirm/showPrompt 已可用');
});
```

---

## 样式和 UI

### CSS 文件（推荐）

在组件目录下创建同名的 CSS 文件：

```css
/* packages/components/src/MyComponent.css */
.my-component {
  padding: 20px;
  background: #f0f0f0;
}

.my-component__title {
  font-size: 24px;
  color: #333;
}
```

在组件中导入：

```typescript
// packages/components/src/MyComponent.tsx
import "./MyComponent.css";

export function MyComponent() {
  return (
    <div className="my-component">
      <h1 className="my-component__title">标题</h1>
    </div>
  );
}
```

### 内联样式

```typescript
const style: React.CSSProperties = {
  padding: "20px",
  backgroundColor: "#f0f0f0"
};

<div style={style}>内容</div>
```

### 全局样式

在 `src/web/styles.css` 中定义全局样式：

```css
/* src/web/styles.css */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
}
```

在 `src/web/App.tsx` 中导入：

```typescript
import "./styles.css";
```

---

## 测试

### 运行测试

请求库包含完整的单元测试套件，使用 Vitest 编写：

```bash
# 运行请求库测试
cd frontend/packages/request && npm test

# 或在 frontend 目录下使用 workspace 命令
cd frontend && npm run test -w @project_neko/request
```

### 测试覆盖率

生成测试覆盖率报告：

```bash
cd frontend/packages/request && npx vitest run --coverage
```

覆盖率报告将生成到 `packages/request/coverage/` 目录。

### 测试文件说明

| 文件 | 描述 |
|------|------|
| `requestClient.test.ts` | 请求客户端核心功能测试（Token 刷新、请求队列、拦截器等） |
| `entrypoints.test.ts` | 入口文件导出测试（index.ts、index.web.ts、index.native.ts） |
| `nativeStorage.test.ts` | React Native 存储抽象测试 |

### 编写测试

使用 Vitest 编写测试：

```typescript
// packages/request/__tests__/myTest.test.ts
import { describe, expect, it, vi, beforeEach, afterEach } from "vitest";
import { createRequestClient } from "../createClient";
import type { TokenStorage } from "../src/request-client/types";

// 创建内存存储用于测试
const createMemoryStorage = (): TokenStorage => {
  let accessToken: string | null = null;
  let refreshToken: string | null = null;

  return {
    async getAccessToken() { return accessToken; },
    async setAccessToken(token: string) { accessToken = token; },
    async getRefreshToken() { return refreshToken; },
    async setRefreshToken(token: string) { refreshToken = token; },
    async clearTokens() { accessToken = null; refreshToken = null; }
  };
};

describe("我的测试", () => {
  afterEach(() => {
    vi.restoreAllMocks();
  });

  it("应该正常工作", async () => {
    const storage = createMemoryStorage();
    const client = createRequestClient({
      baseURL: "/api",
      storage,
      refreshApi: vi.fn()
    });

    expect(client).toBeDefined();
  });
});
```

---

## 构建和部署

### 构建模式

项目支持两种构建模式：

- **开发构建** (`build:dev`)：用于本地开发和调试
  - 启用请求日志
  - 生成 sourcemap 便于调试
  - 不压缩代码
  
- **生产构建** (`build:prod`)：用于生产环境部署
  - 禁用请求日志
  - 不生成 sourcemap（减小体积）
  - 压缩代码

### 构建生产版本

执行完整构建流程：

```bash
cd frontend
npm run build
# 或
npm run build:prod
```

执行开发构建：

```bash
cd frontend
npm run build:dev
```

构建流程依次执行：

1. **`clean:bundles`**：清空 `static/bundles` 目录
2. **`build:request`**：构建请求库，产出 ES/UMD 双格式
3. **`build:common`**：构建通用工具包，产出 ES/UMD 双格式
4. **`build:components`**：构建组件库，产出 ES/UMD 双格式，外部化 `react`/`react-dom`
5. **`build:web-bridge`**：构建桥接层，产出 ES/UMD 双格式
6. **`build:web`**：构建 Web 应用入口，生成 `react_web.js`（ES 模块）
7. **`copy:react-umd`**：复制 React UMD 文件到 `static/bundles`

### 构建产物

主要产物位于 `static/bundles/`（仓库根目录）：

- **`components.js`** / **`components.es.js`**：组件库（UMD/ES 格式）
- **`components.css`**：组件库样式文件
- **`common.js`** / **`common.es.js`**：通用工具（UMD/ES 格式）
- **`request.js`** / **`request.es.js`**：请求库（UMD/ES 格式）
- **`web-bridge.js`** / **`web-bridge.es.js`**：桥接层（UMD/ES 格式）
- **`react.production.min.js`**：React 生产环境 UMD
- **`react-dom.production.min.js`**：ReactDOM 生产环境 UMD

**`dist/webapp/`**（frontend 目录下）：
- **`react_web.js`**：SPA 入口（ESM 格式）
- **`frontend.css`**：Web 应用样式文件

### 服务端集成

在服务端模板中按以下顺序引用构建产物：

```html
<!-- 1. 引入请求库（不依赖 React） -->
<script src="/static/bundles/request.js"></script>

<!-- 2. 引入桥接层（自动绑定 window.request） -->
<script src="/static/bundles/web-bridge.js"></script>

<!-- 3. 引入 React/ReactDOM UMD（组件库依赖） -->
<script src="/static/bundles/react.production.min.js"></script>
<script src="/static/bundles/react-dom.production.min.js"></script>

<!-- 4. 引入组件库样式 -->
<link rel="stylesheet" href="/static/bundles/components.css" />

<!-- 5. 引入组件库 UMD（依赖全局 React/ReactDOM） -->
<script src="/static/bundles/components.js"></script>
```

确保页面中存在 `<div id="root"></div>` 作为挂载点（使用 SPA 时）。

### 单独构建

可以单独构建某个包：

```bash
# 构建组件库
npm run build:components       # 生产模式
npm run build:components:dev   # 开发模式

# 构建请求库
npm run build:request
npm run build:request:dev

# 构建通用工具
npm run build:common
npm run build:common:dev

# 构建桥接层
npm run build:web-bridge
npm run build:web-bridge:dev

# 构建 Web 应用
npm run build:web
npm run build:web:dev
```

---

## 调试技巧

### React DevTools

安装 [React Developer Tools](https://react.dev/learn/react-developer-tools) 浏览器扩展，可以：
- 查看组件树
- 检查组件 props 和 state
- 性能分析

### 控制台调试

```typescript
console.log("调试信息", variable);
console.error("错误信息", error);
console.table(data); // 表格形式显示数据
console.group("分组", data); // 分组显示
```

### 断点调试

在代码中添加 `debugger;` 语句，浏览器会在该处暂停：

```typescript
function MyComponent() {
  debugger; // 浏览器会在此处暂停
  return <div>内容</div>;
}
```

### TypeScript 类型检查

运行类型检查，不生成文件：

```bash
cd frontend
npm run typecheck
```

### Vite 开发工具

Vite 提供了丰富的开发工具：
- **HMR（热模块替换）**：修改代码后自动更新
- **错误覆盖**：在浏览器中显示编译错误
- **网络面板**：查看请求详情

### 常见问题排查

1. **构建失败**
   - 检查 Node.js 和 npm 版本是否符合要求
   - 确保已执行 `npm install`
   - 检查 `static/bundles` 目录权限

2. **开发服务器无法启动**
   - 检查端口是否被占用（默认 5173）
   - 确认 `vite.web.config.ts` 配置正确

3. **类型错误**
   - 运行 `npm run typecheck` 查看详细错误
   - 检查路径别名是否正确配置

4. **运行时错误**
   - 检查浏览器控制台错误信息
   - 确认服务端模板中脚本引用顺序正确
   - 验证 `#root` 元素是否存在

5. **测试失败**
   - 确保在 `packages/request` 目录下运行测试
   - 检查是否有未安装的开发依赖
   - 查看测试输出中的具体错误信息

---

## 相关文档

在开始前端开发之前，建议先阅读：

- [环境搭建指南](./01-getting-started.md) - 环境配置和项目启动
- [项目架构与目录结构](./03-project-structure.md) - 了解项目结构
- [开发实战入门](./04-development-basics.md) - 完成第一个功能

## 下一步

完成前端开发学习后，可以继续学习：

- [后端开发教程](./06-backend-development.md) - 学习后端开发
---

**遇到问题？** 在 [GitHub Issues](https://github.com/Project-N-E-K-O/nekodemy/issues) 提交你的问题，我们会及时解答！

