# 开发实战入门

通过实际案例学习 Project N.E.K.O. 的开发流程，完成你的第一个功能开发：从添加 API 端点到修改前端界面，再到提交代码。

## 📋 目录

- [准备工作](#准备工作)
- [启动项目](#启动项目)
- [第一个任务：添加一个简单的 API 端点](#第一个任务添加一个简单的-api-端点)
- [第一个任务：修改前端显示](#第一个任务修改前端显示)
- [测试你的修改](#测试你的修改)
- [提交代码](#提交代码)

---

## 准备工作

在开始开发之前，请确保你已经完成了环境搭建。如果你还没有完成，请先参考：

- [环境搭建指南](./01-getting-started.md) - 环境配置、依赖安装、服务器启动
- [Git 与 GitHub 完整指南](./02-git-github-guide.md) - 项目克隆、分支管理、代码提交
- [项目架构与目录结构](./03-project-structure.md) - 了解项目目录结构和核心模块

> 💡 **提示**：N.E.K.O 项目使用 `uv` 管理 Python 依赖，无需手动创建虚拟环境。使用 `uv sync` 会自动安装所有依赖。详见 [环境搭建指南 - 配置 Python 项目环境](./01-getting-started.md#4-配置-python-项目环境)。

---

## 启动项目

关于如何启动项目服务器，请参考 [环境搭建指南 - 启动服务器](./01-getting-started.md#6-启动服务器)。

简要说明：
- **记忆服务器**：`uv run python memory_server.py`（端口 48912）
- **主服务器**：`uv run python main_server.py`（端口 48911）
- **访问 Web 界面**：`http://localhost:48911`

---

## 第一个任务：添加一个简单的 API 端点

让我们添加一个简单的健康检查 API 端点。

> 💡 **提示**：关于项目结构和文件位置，请参考 [项目架构与目录结构 - 开发入口点](./03-project-structure.md#开发入口点)。

### 1. 找到主服务器文件

打开 `main_server.py`，找到路由定义部分（通常在文件中间位置，搜索 `@app.get` 或 `@app.post`）。

### 2. 添加新的路由

N.E.K.O 使用 **FastAPI** 框架，路由使用装饰器定义。在适当的位置添加：

```python
from fastapi import FastAPI
from datetime import datetime

@app.get("/api/health")
async def health_check():
    """健康检查端点"""
    return {
        "status": "ok",
        "timestamp": datetime.now().isoformat(),
        "version": "1.0.0"
    }
```

**注意**：
- FastAPI 使用 `@app.get()`、`@app.post()` 等装饰器
- 函数可以是 `async` 异步函数
- 直接返回字典，FastAPI 会自动转换为 JSON

### 3. 测试 API

启动服务器后，访问：
- 浏览器：`http://localhost:48911/api/health`
- 或使用 curl：
  ```bash
  curl http://localhost:48911/api/health
  ```

应该看到 JSON 响应。

---

## 第一个任务：修改前端显示

让我们修改前端，显示一些自定义信息。

> 💡 **提示**：关于前端目录结构，请参考 [项目架构与目录结构 - 前端模块](./03-project-structure.md#前端模块)。

### 方法一：修改 React 前端（推荐）

1. **进入前端目录**
   ```bash
   cd frontend
   ```

2. **安装依赖**
   ```bash
   npm install
   ```

3. **找到主页面**
   打开 `frontend/src/web/App.tsx` 或相关组件文件

4. **添加自定义内容**
   在适当位置添加你的组件或文本

5. **构建前端**
   ```bash
   npm run build
   ```

6. **查看效果**
   刷新浏览器页面

### 方法二：修改传统前端

1. **找到 HTML 模板**
   打开 `templates/index.html`（详见 [项目架构与目录结构 - HTML 模板](./03-project-structure.md#html-模板-templates)）

2. **添加自定义内容**
   在适当位置添加 HTML

3. **查看效果**
   刷新浏览器页面

---

## 测试你的修改

### 后端 API 测试

使用 curl 或 Postman 测试你的 API：

```bash
# 测试健康检查端点
curl http://localhost:48911/api/health

# 测试其他端点
curl http://localhost:48911/api/your-endpoint
```

### 前端测试

1. 启动开发服务器（React）：
   ```bash
   cd frontend
   npm run dev
   ```

2. 在浏览器中测试功能

3. 检查浏览器控制台是否有错误

---

## 提交代码

关于 Git 操作和 Pull Request 流程，请参考 [Git 与 GitHub 完整指南](./02-git-github-guide.md)。

简要流程：

1. **创建开发分支**
   ```bash
   git checkout -b feature/my-first-feature
   ```

2. **暂存和提交变更**
   ```bash
   git add .
   git commit -m "feat: 添加健康检查 API 端点"
   ```

3. **推送到 GitHub 并创建 PR**
   ```bash
   git push -u origin feature/my-first-feature
   ```
   然后在 GitHub 上创建 Pull Request

> 💡 **提示**：关于提交信息规范和 PR 模板，请参考 [Git 与 GitHub 完整指南 - 基本开发流程](./02-git-github-guide.md#基本开发流程)。

---

## 开发工作流总结

完整的开发工作流程请参考 [Git 与 GitHub 完整指南 - 基本开发流程](./02-git-github-guide.md#基本开发流程)。

简要总结：

```bash
# 1. 同步主分支（参考 02 文档的场景二）
git checkout main
git pull origin main  # 或从 neko 远程拉取

# 2. 创建功能分支
git checkout -b feature/my-feature

# 3. 开发功能
# ... 编写代码 ...

# 4. 测试
# ... 运行测试 ...

# 5. 提交
git add .
git commit -m "feat: 描述你的变更"
git push -u origin feature/my-feature

# 6. 创建 PR
# 在 GitHub 上创建 Pull Request
```

---

## 常见开发任务

### 添加新的 API 端点

1. 在 `main_server.py` 中添加路由
2. 实现处理函数
3. 返回 JSON 响应
4. 测试端点

### 修改前端 UI

1. 如果是 React：修改 `frontend/` 中的组件（详见 [项目架构与目录结构 - React 前端](./03-project-structure.md#react-前端-frontend)）
2. 如果是传统前端：修改 `templates/` 和 `static/`（详见 [项目架构与目录结构 - HTML 模板](./03-project-structure.md#html-模板-templates) 和 [静态资源](./03-project-structure.md#静态资源-static)）
3. 构建并测试

### 添加新的配置项

1. 在 `config/` 目录中添加配置（详见 [项目架构与目录结构 - 配置管理](./03-project-structure.md#配置管理-config)）
2. 在代码中读取配置
3. 更新文档

> 💡 **注意**：核心配置（API Key 等）通过环境变量或 Docker 配置管理，不再使用 `core_config.json` 文件。详见 [项目架构与目录结构 - Config 模块](./03-project-structure.md#5-config-模块配置管理)。

### 修改 AI 提示词

1. 修改 `config/prompts_chara.py`（角色提示词）
2. 或修改 `config/prompts_sys.py`（系统提示词）
3. 重启服务器测试效果

---

## 调试技巧

### Python 调试

使用 `print()` 或日志：

```python
import logging
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.debug("调试信息")
logger.info("一般信息")
logger.error("错误信息")
```

### 前端调试

1. 打开浏览器开发者工具（F12）
2. 查看 Console 标签页的错误
3. 使用 `console.log()` 输出调试信息

### 查看服务器日志

服务器运行时会输出日志，注意查看终端输出。

---

## 相关文档

在开始开发之前，建议先阅读：

- [环境搭建指南](./01-getting-started.md) - 环境配置和项目启动
- [Git 与 GitHub 完整指南](./02-git-github-guide.md) - 代码管理和协作流程
- [项目架构与目录结构](./03-project-structure.md) - 项目架构和目录结构

## 下一步

完成第一个功能后，可以深入学习：

- [前端开发教程](./05-frontend-development.md) - 深入学习前端开发
- [后端开发教程](./06-backend-development.md) - 深入学习后端开发
---

**遇到问题？** 在 [GitHub Issues](https://github.com/Project-N-E-K-O/nekodemy/issues) 提交你的问题，我们会及时解答！

