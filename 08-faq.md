# 常见问题 FAQ

开发过程中常见问题的快速解答，涵盖环境配置、项目启动、开发流程等各个方面。

## 📋 目录

- [环境配置](#环境配置)
- [项目启动](#项目启动)
- [开发相关](#开发相关)
- [Git/GitHub](#gitgithub)
- [API 配置](#api-配置)
- [前端开发](#前端开发)
- [后端开发](#后端开发)
- [文档维护](#文档维护)

---

## 环境配置

> 💡 **提示**：关于环境配置的详细说明和常见问题，请参考 [环境搭建指南 - 常见问题](./01-getting-started.md#常见问题)。

### Q: 需要先安装 Python 吗？

**A**: **不需要！** N.E.K.O 项目使用 `uv` 管理 Python 依赖，`uv` 可以自动安装和管理 Python 版本。详见 [环境搭建指南 - 常见问题](./01-getting-started.md#q-需要先安装-python-吗)。

### Q: Python 命令找不到？

**A**: 如果你使用 `uv` 管理的 Python，可以通过虚拟环境或 `uv run` 命令使用。详见 [环境搭建指南 - 常见问题](./01-getting-started.md#q-python-命令找不到)。

### Q: uv 命令找不到？

**A**: 检查安装和 PATH 配置。详见 [环境搭建指南 - 常见问题](./01-getting-started.md#q-uv-命令找不到)。

### Q: uv sync 失败？

**A**: 检查网络连接、配置镜像或磁盘空间。详见 [环境搭建指南 - 常见问题](./01-getting-started.md#q-uv-sync-失败)。

### Q: 虚拟环境激活失败？

**A**: Windows 可能需要设置执行策略，macOS/Linux 确保使用正确的路径。详见 [环境搭建指南 - 常见问题](./01-getting-started.md#q-虚拟环境激活失败)。

### Q: Node.js 版本要求是多少？

**A**: 最低支持 Node.js 18.x，推荐使用 Node.js 20.x 或 22.x（最新 LTS 版本）。详见 [环境搭建指南](./01-getting-started.md#7-安装-nodejs前端开发需要)。

---

## 项目启动

> 💡 **提示**：关于服务器启动的详细说明，请参考 [环境搭建指南 - 启动服务器](./01-getting-started.md#6-启动服务器)。

### Q: 需要启动哪些服务器？

**A**: 至少需要启动两个服务器：记忆服务器（端口 48912）和主服务器（端口 48911）。详见 [环境搭建指南 - 启动服务器](./01-getting-started.md#6-启动服务器)。

### Q: 端口被占用怎么办？

**A**: 修改配置文件中的端口设置，或关闭占用端口的程序。

### Q: 启动后无法访问 Web 界面？

**A**: 检查服务器状态、端口配置、防火墙设置和访问地址。默认访问地址：`http://localhost:48911`。

### Q: 使用 Docker 部署？

**A**: N.E.K.O 项目支持 Docker 部署，使用 docker-compose 可以一键启动所有服务。详见项目 README 中的 Docker 部署章节。

---

## 开发相关

> 💡 **提示**：关于开发流程的详细说明，请参考 [开发实战入门](./04-development-basics.md)。

### Q: 如何添加新的 API 端点？

**A**: 在 `main_server.py` 中添加 FastAPI 路由。详见 [开发实战入门 - 第一个任务：添加一个简单的 API 端点](./04-development-basics.md#第一个任务添加一个简单的-api-端点) 和 [后端开发教程 - API 开发指南](./06-backend-development.md#api-开发指南)。

### Q: 如何修改 AI 的性格？

**A**: 编辑 `config/prompts_chara.py` 文件，修改角色提示词。修改后需要重启服务器才能生效。

### Q: 如何添加新的 Live2D 模型？

**A**: 将模型文件复制到 `static/` 目录，在 Web 界面中通过 Live2D 管理页面配置。

### Q: 项目使用什么框架？

**A**: 后端使用 FastAPI，前端使用 React 19 + Vite 7 + TypeScript。详见 [项目架构与目录结构 - 项目概览](./03-project-structure.md#项目概览)。

---

## Git/GitHub

> 💡 **提示**：关于 Git 和 GitHub 的详细操作指南，请参考 [Git 与 GitHub 完整指南](./02-git-github-guide.md)。

### Q: 如何同步 Fork 的更新？

**A**: 推荐在 GitHub 上同步，或通过命令行同步。详见 [Git 与 GitHub 完整指南 - 同步 Fork](./02-git-github-guide.md#场景二非初次-clone已存在本地仓库)。

### Q: 如何撤销最后一次提交？

**A**: 使用 `git reset --soft HEAD~1`（保留变更）或 `git reset --hard HEAD~1`（完全撤销）。详见 [Git 与 GitHub 完整指南 - 撤销和重写历史](./02-git-github-guide.md#撤销和重写历史)。

> ⚠️ **注意**：这是进阶操作，如果已经推送到远程，撤销后需要强制推送，可能会影响其他协作者。

### Q: 如何解决合并冲突？

**A**: 手动解决冲突标记，暂存文件后完成合并。详见 [Git 与 GitHub 完整指南 - 常见问题](./02-git-github-guide.md#q-如何解决合并冲突进阶)。

### Q: 如何创建 Pull Request？

**A**: Fork 项目、创建功能分支、提交代码、推送到 Fork，然后在 GitHub 上创建 PR。详见 [Git 与 GitHub 完整指南 - 基本开发流程](./02-git-github-guide.md#场景一初次-clone-项目)。

---

## API 配置

### Q: 如何配置 API Key？

**A**: 启动项目后访问 `http://localhost:48911/api_key` 在 Web 界面中配置，或通过环境变量配置（Docker 部署时）。

> 💡 **注意**：核心配置（API Key 等）通过环境变量或 Docker 配置管理，不再使用 `core_config.json` 文件。详见 [项目架构与目录结构 - Config 模块](./03-project-structure.md#5-config-模块配置管理)。

### Q: 支持哪些 API 服务商？

**A**: 查看 `config/api_providers.json` 了解支持的服务商。详见 [项目架构与目录结构 - 配置文件说明](./03-project-structure.md#配置文件说明)。

### Q: API Key 存储在哪里？

**A**: 本地开发通过 Web 界面配置，Docker 部署通过环境变量配置。注意不要将包含 API Key 的配置文件提交到 Git。

---

## 前端开发

> 💡 **提示**：关于前端开发的详细说明，请参考 [前端开发教程](./05-frontend-development.md)。

### Q: React 前端如何开发？

**A**: 进入 `frontend/` 目录，运行 `npm install` 和 `npm run dev:web`。详见 [前端开发教程 - React 前端开发](./05-frontend-development.md#react-前端开发)。

### Q: 如何添加新的 React 组件？

**A**: 在 `frontend/packages/components/src/` 目录下创建组件文件并导出。详见 [前端开发教程 - 创建新组件](./05-frontend-development.md#创建新组件)。

### Q: 如何修改前端样式？

**A**: React 组件使用同名 CSS 文件或内联样式，传统前端修改 `static/` 目录下的 CSS 文件。详见 [前端开发教程 - 样式和 UI](./05-frontend-development.md#样式和-ui)。

### Q: 前端目录结构是什么？

**A**: 使用 npm workspaces 管理多个子包（components、request、common）。详见 [项目架构与目录结构 - 前端模块](./03-project-structure.md#前端模块)。

---

## 后端开发

> 💡 **提示**：关于后端开发的详细说明，请参考 [后端开发教程](./06-backend-development.md)。

### Q: 如何添加新的工具函数？

**A**: 在 `utils/` 目录下创建新的 Python 模块。详见 [项目架构与目录结构 - Utils 模块](./03-project-structure.md#7-utils-模块工具函数)。

### Q: 如何修改对话逻辑？

**A**: 编辑 `main_helper/core.py` 文件。详见 [项目架构与目录结构 - Main Helper 模块](./03-project-structure.md#3-main-helper-模块核心对话)。

### Q: 如何添加新的 AI 能力？

**A**: 在 `brain/` 目录下添加新的模块，或修改现有模块。详见 [项目架构与目录结构 - Brain 模块](./03-project-structure.md#2-brain-模块ai-智能体)。

### Q: 后端使用什么框架？

**A**: **FastAPI**（不是 Flask）。所有路由使用 `@app.get()`、`@app.post()` 等装饰器，支持异步处理。详见 [后端开发教程 - FastAPI 服务器开发](./06-backend-development.md#fastapi-服务器开发)。

### Q: 如何访问记忆服务器？

**A**: 记忆服务器运行在独立端口，通过 HTTP 请求访问。可以使用 `main_helper.cross_server` 模块简化跨服务器通信。详见 [后端开发教程 - 记忆系统使用](./06-backend-development.md#记忆系统使用)。

---

## 文档维护

> 💡 **提示**：关于文档维护的详细说明和提示词模板，请参考 [文档维护与同步指南](./07-document-maintenance.md)。

### Q: 如何使用 AI 编辑器维护文档？

**A**: 推荐使用 Cursor 等 AI 编辑器，通过关键提示词模板快速更新文档。详见 [文档维护与同步指南](./07-document-maintenance.md)。

### Q: 如何同步文档与项目代码？

**A**: 使用文档维护指南中的提示词模板，根据 N.E.K.O 项目更新相关文档。详见 [文档维护与同步指南 - 关键提示词模板](./07-document-maintenance.md#关键提示词模板)。

---

## 其他问题

### Q: 项目使用什么许可证？

**A**: MIT 许可证。查看项目根目录的 `LICENSE` 文件。

### Q: 如何贡献代码？

**A**: 
1. Fork 项目
2. 创建功能分支
3. 提交代码
4. 创建 Pull Request

详细流程参考 [Git 与 GitHub 完整指南](./02-git-github-guide.md)。

### Q: 遇到问题如何求助？

**A**: 
1. 查看 [GitHub Issues](https://github.com/Project-N-E-K-O/nekodemy/issues)
2. 在 GitHub 上创建 Issue
3. 加入 QQ 群：1022939659

### Q: 如何开始学习开发？

**A**: 建议按照以下顺序学习：
1. [环境搭建指南](./01-getting-started.md) - 配置开发环境
2. [Git 与 GitHub 完整指南](./02-git-github-guide.md) - 掌握版本控制
3. [项目架构与目录结构](./03-project-structure.md) - 了解项目结构
4. [开发实战入门](./04-development-basics.md) - 完成第一个功能

详见 [README - 学习路径](./README.md#学习路径)。

---

**没有找到答案？** 在 [GitHub Issues](https://github.com/Project-N-E-K-O/nekodemy/issues) 提交你的问题！
