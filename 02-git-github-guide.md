# Git 与 GitHub 完整指南

本指南将帮助你从零开始掌握 Git 和 GitHub，包括基础操作、进阶技巧和常见问题解答。

## 📋 目录

### 第一部分：基础入门
- [Git 基础概念](#第一部分基础入门)
- [GitHub 账号设置](#github-账号设置)
- [命令行 vs 图形界面操作](#命令行-vs-图形界面操作)（可选了解）
- [Fork 和克隆项目](#fork-和克隆项目)
- [基本开发流程](#基本开发流程)
  - [场景一：初次 Clone 项目](#场景一初次-clone-项目)
  - [场景二：非初次 Clone（已存在本地仓库）](#场景二非初次-clone已存在本地仓库)

### 第二部分：进阶操作
- [Merge vs Rebase](#merge-vs-rebase)
- [Squash（压缩提交）](#squash压缩提交)
- [Reflog（引用日志）](#reflog引用日志)
- [Cherry-pick（拣选提交）](#cherry-pick拣选提交)
- [撤销和重写历史](#撤销和重写历史)
- [实用 Git 命令速查](#实用-git-命令速查)

### 第三部分：常见问题（QA）
- [常见问题](#第三部分常见问题qa)

---

## 第一部分：基础入门

### Git 基础概念

### 什么是 Git？

Git 是一个**分布式版本控制系统**，用于跟踪代码的变化历史。

### 核心概念

- **仓库（Repository）**：项目的代码存储位置
- **提交（Commit）**：代码的一个快照，包含变更说明
- **分支（Branch）**：代码的独立开发线
- **远程（Remote）**：远程代码仓库（如 GitHub）

---

## GitHub 账号设置

### 1. 注册 GitHub 账号

1. 访问 [GitHub 官网](https://github.com/)
2. 点击 "Sign up" 注册账号
3. 完成邮箱验证

### 2. 配置 SSH 密钥（推荐）

SSH 密钥可以让你无需每次输入密码。

#### 生成 SSH 密钥

**Windows (Git Bash) / macOS / Linux:**

```bash
# 生成密钥（替换为你的邮箱）
ssh-keygen -t ed25519 -C "your_email@example.com"

# 按 Enter 使用默认路径
# 可以设置密码或直接按 Enter（不设置密码）
```

#### 添加 SSH 密钥到 GitHub

1. 复制公钥内容：

**Windows (Git Bash):**
```bash
cat ~/.ssh/id_ed25519.pub
```

**macOS / Linux:**
```bash
cat ~/.ssh/id_ed25519.pub
```

2. 登录 GitHub → Settings → SSH and GPG keys → New SSH key
3. 粘贴公钥内容，保存

#### 测试连接

```bash
ssh -T git@github.com
```

应该看到 "Hi username! You've successfully authenticated..."

### 3. 配置 Git 用户信息

```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "your_email@example.com"
```

---

## 命令行 vs 图形界面操作

Git 操作可以通过两种方式进行：**命令行（Terminal）** 和 **图形界面（GUI，如 VSCode/Cursor）**。本教程主要介绍命令行操作，但也会说明图形界面的对应操作。

### 两种方式对比

| 特性 | 命令行 | VSCode/Cursor GUI |
|------|--------|-------------------|
| **学习曲线** | 需要记忆命令 | 可视化，更直观 |
| **操作速度** | 快速，适合批量操作 | 需要鼠标点击，稍慢 |
| **功能完整性** | 支持所有 Git 功能 | 支持常用功能，部分高级功能需要命令行 |
| **跨平台一致性** | 完全一致 | 界面可能略有差异 |
| **推荐场景** | 日常开发、CI/CD、自动化脚本 | 代码审查、查看历史、新手入门 |

### VSCode/Cursor 中的 Git 操作

在 VSCode 或 Cursor 中，你可以通过以下方式进行 Git 操作：

#### 1. 查看变更状态

**命令行：**
```bash
git status
```

**GUI：**
- 打开源代码管理面板（点击左侧边栏的源代码管理图标，或按 `Ctrl+Shift+G` / `Cmd+Shift+G`）
- 可以看到所有修改、新增、删除的文件

#### 2. 查看文件变更内容

**命令行：**
```bash
git diff
# 或查看特定文件
git diff path/to/file.py
```

**GUI：**
- 在源代码管理面板中点击文件名
- 会显示文件的具体变更（绿色=新增，红色=删除）

#### 3. 暂存文件（git add）

**命令行：**
```bash
git add .
# 或暂存特定文件
git add path/to/file.py
```

**GUI：**
- 在源代码管理面板中，修改的文件旁边有 `+` 号
- 点击 `+` 号或文件右侧的 "+" 按钮即可暂存
- 或点击 "Changes" 标题旁的 `+` 号暂存所有变更

#### 4. 提交变更（git commit）

**命令行：**
```bash
git commit -m "feat: 添加新功能"
```

**GUI：**
- 在源代码管理面板顶部的输入框输入提交信息
- 点击输入框右侧的 ✓ 按钮（或按 `Ctrl+Enter` / `Cmd+Enter`）提交
- 注意：GUI 会自动暂存所有修改的文件（相当于 `git add .` + `git commit`）

#### 5. 推送（git push）

**命令行：**
```bash
git push origin feature-branch
```

**GUI：**
- 提交后，点击源代码管理面板右上角的 "..." 菜单
- 选择 "Push" 或 "Push to..."
- 或使用快捷键（通常在状态栏有推送按钮）

#### 6. 切换分支

**命令行：**
```bash
git checkout feature-branch
# 或创建新分支
git checkout -b feature-branch
```

**GUI：**
- 点击左下角的状态栏，会显示当前分支
- 点击后可以选择切换分支或创建新分支
- 或在命令面板（`Ctrl+Shift+P` / `Cmd+Shift+P`）输入 "Git: Checkout to..." 或 "Git: Create Branch..."

#### 7. 拉取更新（git pull）

**命令行：**
```bash
git pull origin main
```

**GUI：**
- 点击源代码管理面板的 "..." 菜单
- 选择 "Pull" 或 "Pull from..."
- 或在命令面板输入 "Git: Pull"

### 使用建议

- **新手入门**：建议先从 GUI 开始，熟悉基本操作后再学习命令行
- **日常开发**：可以混合使用，GUI 查看变更，命令行快速提交
- **复杂操作**：某些高级操作（如 rebase、cherry-pick）建议使用命令行，GUI 可能不够直观
- **学习 Git**：建议掌握命令行操作，这样可以：
  - 理解每个操作的底层原理
  - 在服务器或远程环境中也能操作
  - 更好地理解 CI/CD 流程

### 本教程的侧重点

本教程主要介绍**命令行操作**，因为：
1. 命令行是 Git 的基础，掌握了命令行就能理解 GUI 背后的原理
2. 命令行操作在所有环境下都可用
3. 有助于理解 Git 的工作流程

但你可以随时在 VSCode/Cursor 中通过 GUI 完成相同的操作。

---

### Fork 和克隆项目

#### Fork 项目（首次参与）

1. 访问 [Project N.E.K.O. 项目仓库](https://github.com/Project-N-E-K-O/N.E.K.O)
2. 点击右上角 "Fork" 按钮
3. 这会在你的账号下创建一个副本

#### 远程仓库说明

在 N.E.K.O 项目中，我们使用以下命名约定：
- **`origin`**：你自己的 Fork 仓库（`git@github.com:你的用户名/N.E.K.O.git`）
- **`neko`**：主项目仓库（`https://github.com/Project-N-E-K-O/N.E.K.O.git`）

---

### 基本开发流程

#### 场景一：初次 Clone 项目

### 1. 克隆你的 Fork 到本地

```bash
# 克隆你 Fork 的仓库（使用 SSH，替换为你的用户名）
git clone git@github.com:你的用户名/N.E.K.O.git

# 进入项目目录
cd N.E.K.O
```

**注意**：确保已配置 SSH 密钥（参考前面的 [SSH 密钥配置](#2-配置-ssh-密钥推荐)）

### 2. 配置远程仓库

```bash
# 确认 origin 指向你的 Fork
git remote -v

# 添加主项目远程仓库（命名为 neko，使用 HTTPS）
git remote add neko https://github.com/Project-N-E-K-O/N.E.K.O.git

# 验证远程仓库配置
git remote -v
```

应该看到：
```
origin  git@github.com:你的用户名/N.E.K.O.git (fetch)
origin  git@github.com:你的用户名/N.E.K.O.git (push)
neko    https://github.com/Project-N-E-K-O/N.E.K.O.git (fetch)
neko    https://github.com/Project-N-E-K-O/N.E.K.O.git (push)
```

### 3. 创建功能分支

```bash
# 确保在主分支
git checkout main

# 创建新的功能分支
git checkout -b feature-branch

# 或使用更简洁的命令
git switch -c feature-branch
```

**分支命名建议：**
- `feature/xxx` - 新功能
- `fix/xxx` - 修复 Bug
- `docs/xxx` - 文档更新
- `refactor/xxx` - 代码重构

### 4. 进行开发改动

在分支上进行代码修改...

### 5. 查看和暂存变更

```bash
# 查看修改的文件
git status

# 查看具体的变更内容
git diff

# 暂存所有变更
git add .

# 或暂存特定文件
git add path/to/file.py
```

### 6. 提交变更

```bash
# 提交变更（使用规范的提交信息）
git commit -m "feat: 添加新功能描述"
```

**提交信息规范（遵循开源标准）：**
- 格式：`<type>: <description>`
- 类型（type）：
  - `feat`: 新功能
  - `fix`: 修复 Bug
  - `docs`: 文档更新
  - `refactor`: 代码重构
  - `style`: 代码格式调整（不影响功能）
  - `test`: 测试相关
  - `chore`: 构建/工具相关

示例：
```bash
git commit -m "feat: 添加 Live2D 模型加载功能"
git commit -m "fix: 修复内存泄漏问题"
git commit -m "docs: 更新 API 文档"
```

### 7. 推送到你的 Fork

```bash
git push origin feature-branch
```

### 8. 创建 Pull Request

1. 访问你的 Fork 仓库页面（`https://github.com/你的用户名/N.E.K.O`）
2. 点击 "Compare & pull request" 按钮
3. 填写 PR 说明：
   - **标题**：清晰描述你的变更
   - **描述**：详细说明做了什么、为什么做、如何测试

**PR 模板示例：**

```markdown
## 变更说明
- 添加了 XXX 功能
- 修复了 XXX 问题

## 测试方法
1. 启动项目
2. 执行 XXX 操作
3. 验证结果

## 截图（如适用）
[添加截图]

## 相关 Issue
Closes #123
```

### 9. PR 合并后清理分支

PR 被合并后，删除本地和远程分支：

```bash
# 切换回主分支
git checkout main

# 在 GitHub 上同步 Fork（参考场景二的步骤 1）
# 然后在本地拉取更新
git pull origin main

# 删除本地功能分支
git branch -d feature-branch

# 如果分支未完全合并，使用 -D 强制删除
git branch -D feature-branch
```

远程分支会在 PR 合并时自动删除（如果仓库设置了自动删除）。

---

#### 场景二：非初次 Clone（已存在本地仓库）

如果你已经有本地仓库，开始新功能开发前需要先同步更新。

### 1. 同步 Fork 的 main 分支

有两种方式可以同步 Fork：

**方法一：在 GitHub 上同步（推荐）**

1. 访问你的 Fork 仓库页面（`https://github.com/你的用户名/N.E.K.O`）
2. 点击 "Sync fork" 或 "Sync" 按钮（如果主项目有更新）
3. 点击 "Update branch" 确认同步

然后在本地拉取更新：
```bash
cd N.E.K.O
git checkout main
git pull origin main
```

**方法二：通过命令行同步**

```bash
# 进入项目目录
cd N.E.K.O

# 确保在主分支
git checkout main

# 从主项目（neko）拉取最新代码
git pull neko main

# 推送到你的 Fork（origin），保持同步
git push origin main
```

### 2. 创建新的功能分支


```bash
# 基于最新的 main 分支创建新分支
git checkout -b feature-branch-new

# 或使用更简洁的命令
git switch -c feature-branch-new
```

### 3. 继续开发流程

接下来的步骤与**场景一**的步骤 4-7 相同：

- 进行开发改动
- 查看和暂存变更（`git add`）
- 提交变更（`git commit -m 'xxx'`）
- 推送到你的 Fork（`git push origin feature-branch-new`）
- 创建 Pull Request
- PR 合并后清理分支

---

## 第二部分：进阶操作

> ⚠️ **重要提示**：本节内容为进阶操作，涉及 Git 的高级功能。建议在掌握基础操作后再学习。操作前请确保理解其影响，并在个人分支上练习。

### Merge vs Rebase

这是 Git 中两种整合分支变更的方式，各有特点。

#### Merge（合并）

**什么是 Merge？**

Merge 会将两个分支的历史记录合并在一起，创建一个新的合并提交（merge commit）。

**使用场景：**
- 将功能分支合并到主分支
- 需要保留完整的分支历史
- 多人协作时更安全

**操作示例：**

```bash
# 切换到主分支
git checkout main

# 合并功能分支
git merge feature-branch

# 推送合并后的主分支
git push origin main
```

**Merge 的特点：**
- ✅ 保留完整的分支历史
- ✅ 不会重写提交历史（安全）
- ✅ 适合多人协作
- ❌ 会产生额外的合并提交
- ❌ 提交历史可能变得复杂（出现分叉）

#### Rebase（变基）

**什么是 Rebase？**

Rebase 会将当前分支的提交"重新应用"到目标分支的最新提交之上，使提交历史变成一条直线。

**使用场景：**
- 保持提交历史的整洁（线性）
- 在合并到主分支前整理提交
- 个人分支整理历史

**操作示例：**

```bash
# 在功能分支上
git checkout feature-branch

# 将主分支的最新提交应用到当前分支之前
git rebase main

# rebase 后需要强制推送（仅在个人分支）
git push -f origin feature-branch
```

**Rebase 的特点：**
- ✅ 提交历史是线性的，更整洁
- ✅ 没有额外的合并提交
- ❌ 会重写提交历史（改变提交的 SHA）
- ❌ 不适合已经共享的分支
- ⚠️ 需要强制推送

**Merge vs Rebase 对比：**

```
Merge 后的历史：
*   Merge commit
|\
| * Feature commit 3
| * Feature commit 2
| * Feature commit 1
|/
* Main commit

Rebase 后的历史：
* Feature commit 3
* Feature commit 2
* Feature commit 1
* Main commit
```

**选择建议：**
- **Merge**：适合将功能分支合并到主分支，多人协作
- **Rebase**：适合个人分支整理，在合并前保持整洁

---

### Squash（压缩提交）

**什么是 Squash？**

Squash 可以将多个提交压缩成一个提交，常用于整理提交历史。

**使用场景：**
- 功能分支有多个小的提交，合并时想合并成一个
- 清理提交历史，使每个提交更有意义
- 在 Pull Request 合并时统一提交信息

**方法一：交互式 Rebase（推荐）**

```bash
# 压缩最近的 3 个提交
git rebase -i HEAD~3

# 或压缩到某个提交之前
git rebase -i <commit-hash>
```

在打开的编辑器中：
1. 将除了第一个 `pick` 之外的其他 `pick` 改为 `squash` 或 `s`
2. 保存并关闭编辑器
3. 在下一个编辑器中编辑合并后的提交信息
4. 保存并关闭

**示例操作：**

```bash
# 假设有 3 个提交要压缩
git rebase -i HEAD~3

# 编辑器显示：
# pick abc123 第一个提交
# pick def456 第二个提交  
# pick ghi789 第三个提交

# 修改为：
# pick abc123 第一个提交
# squash def456 第二个提交
# squash ghi789 第三个提交

# 保存后会打开新的编辑器，编辑最终的提交信息
```

**方法二：使用 GitHub Pull Request 的 Squash 合并**

在创建 Pull Request 时，GitHub 提供了 "Squash and merge" 选项：
1. 在 PR 页面点击 "Merge pull request" 下拉菜单
2. 选择 "Squash and merge"
3. 编辑合并后的提交信息
4. 确认合并

这是最简单的方法，不需要命令行操作。

**注意事项：**
- Squash 会改变提交历史，仅在个人分支或未推送的分支上使用
- 如果已经推送，需要使用 `git push -f` 强制推送

---

### Reflog（引用日志）

**什么是 Reflog？**

Reflog 记录了 HEAD 和分支引用的所有变更历史，可以帮助你找回"丢失"的提交。

**使用场景：**
- 意外删除了分支或提交
- 想要撤销某个操作
- 查找之前的操作历史

**查看 Reflog：**

```bash
# 查看完整的 reflog
git reflog

# 查看特定分支的 reflog
git reflog show branch-name

# 只显示最近 10 条记录
git reflog -10
```

**示例输出：**

```
abc1234 HEAD@{0}: commit: feat: 添加新功能
def5678 HEAD@{1}: checkout: moving from main to feature-branch
ghi9012 HEAD@{2}: commit: fix: 修复 bug
jkl3456 HEAD@{3}: reset: moving to HEAD~1
```

**恢复丢失的提交：**

```bash
# 1. 查看 reflog，找到丢失的提交 hash
git reflog

# 2. 使用提交 hash 或 HEAD@{n} 恢复
git checkout <commit-hash>
# 或
git checkout HEAD@{2}

# 3. 如果需要恢复分支，创建新分支
git checkout -b recovered-branch <commit-hash>
```

**恢复被删除的分支：**

```bash
# 1. 查看 reflog 找到分支最后的位置
git reflog

# 2. 使用 reflog 中的 commit hash 恢复分支
git checkout -b recovered-branch <commit-hash>
```

**注意事项：**
- Reflog 只保留有限时间（默认 90 天）
- Reflog 是本地记录，不会被推送到远程
- 定期清理的仓库可能没有完整的 reflog

---

### Cherry-pick（拣选提交）

**什么是 Cherry-pick？**

Cherry-pick 可以选择性地将某个提交应用到当前分支，不需要合并整个分支。

**使用场景：**
- 只需要某个分支的一个或几个提交
- 将 bug 修复应用到多个分支
- 从其他分支移植特定功能

**基本用法：**

```bash
# 将某个提交应用到当前分支
git cherry-pick <commit-hash>

# 应用多个提交
git cherry-pick <commit-hash-1> <commit-hash-2>

# 应用一个范围内的提交（不包含 start，包含 end）
git cherry-pick <start-commit>..<end-commit>

# 应用一个范围内的提交（包含 start 和 end）
git cherry-pick <start-commit>^..<end-commit>
```

**示例：**

```bash
# 切换到目标分支
git checkout main

# 将功能分支的某个提交应用到主分支
git cherry-pick abc123

# 如果有冲突，解决后继续
git cherry-pick --continue

# 如果想取消 cherry-pick
git cherry-pick --abort
```

**注意事项：**
- Cherry-pick 会创建新的提交（新的 SHA）
- 如果有冲突需要手动解决
- 建议在应用前确保理解提交的内容

---

### 撤销和重写历史

#### 撤销最后一次提交（保留更改）

```bash
# 撤销提交，但保留文件变更
git reset --soft HEAD~1
```

#### 撤销最后一次提交（丢弃更改）

```bash
# ⚠️ 谨慎使用：会丢弃所有变更
git reset --hard HEAD~1
```

#### 修改最后一次提交

```bash
# 修改提交信息
git commit --amend -m "新的提交信息"

# 添加遗漏的文件到最后一次提交
git add forgotten-file.py
git commit --amend --no-edit
```

#### 交互式 Rebase 整理提交

```bash
# 修改最近 5 个提交
git rebase -i HEAD~5
```

在交互式编辑器中可以：
- `pick`：保留提交
- `reword`：修改提交信息
- `edit`：编辑提交
- `squash`：压缩到上一个提交
- `drop`：删除提交

---

### 常用进阶命令速查

```bash
# Merge 相关
git merge <branch>              # 合并分支
git merge --no-ff <branch>      # 创建合并提交（即使可以快进）
git merge --abort                # 取消合并

# Rebase 相关
git rebase <branch>              # 变基到指定分支
git rebase -i HEAD~n             # 交互式 rebase（最近 n 个提交）
git rebase --continue            # 解决冲突后继续
git rebase --abort               # 取消 rebase

# Reflog 相关
git reflog                       # 查看引用日志
git reflog show <branch>         # 查看特定分支的日志

# Cherry-pick 相关
git cherry-pick <commit>         # 应用某个提交
git cherry-pick --continue       # 继续 cherry-pick
git cherry-pick --abort          # 取消 cherry-pick

# Reset 相关
git reset --soft HEAD~1          # 撤销提交，保留变更
git reset --mixed HEAD~1         # 撤销提交和暂存，保留工作区
git reset --hard HEAD~1          # ⚠️ 撤销提交和所有变更
```

---

### 实用 Git 命令速查

```bash
# 查看状态
git status

# 查看变更
git diff

# 暂存文件
git add <file>

# 提交
git commit -m "message"

# 推送
git push

# 拉取
git pull

# 创建分支
git checkout -b <branch-name>

# 切换分支
git checkout <branch-name>

# 查看分支
git branch

# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add <name> <url>

# 示例：添加主项目（neko）
git remote add neko https://github.com/Project-N-E-K-O/N.E.K.O.git

# 查看提交历史
git log --oneline
```

---

## 第三部分：常见问题（QA）

> 💡 **提示**：标有 <small>（进阶）</small> 的问题包含高级操作，新手可以先跳过，需要时再查阅。

### Q: 如何撤销最后一次提交？<small>（进阶）</small>

```bash
# 保留文件变更，只撤销提交
git reset --soft HEAD~1

# 完全撤销提交和变更（谨慎使用）
git reset --hard HEAD~1
```

**注意**：这是进阶操作，新手建议谨慎使用。如果已经推送到远程，撤销后需要强制推送，可能会影响其他协作者。

### Q: 如何修改最后一次提交信息？<small>（进阶）</small>

```bash
git commit --amend -m "新的提交信息"
```

**注意**：如果已经推送到远程，修改后需要强制推送（`git push -f`），仅在个人分支上使用。

### Q: 如何查看提交历史？

```bash
# 简洁版本
git log --oneline

# 图形化版本
git log --graph --oneline --all
```

### Q: 如何丢弃未提交的变更？

```bash
# 丢弃所有未提交的变更（谨慎使用）
git checkout .

# 或
git restore .
```

### Q: 如何解决合并冲突？<small>（进阶）</small>

当你在合并或同步代码时遇到冲突，需要手动解决：

1. 打开冲突文件，找到冲突标记：
   ```
   <<<<<<< HEAD
   你的代码
   =======
   主项目的代码
   >>>>>>> neko/main
   ```

2. 手动解决冲突，删除标记和不需要的代码
3. 暂存解决后的文件：`git add .`
4. 完成合并：`git commit`

**提示**：如果不知道如何处理冲突，可以询问项目维护者或在 GitHub 上创建 Issue 求助。

### Q: 如何同步 Fork 的更新？

**方法一：在 GitHub 上同步（推荐）**

1. 访问你的 Fork 仓库页面
2. 点击 "Sync fork" 或 "Sync" 按钮
3. 点击 "Update branch" 确认同步
4. 在本地拉取更新：

```bash
git checkout main
git pull origin main
```

**方法二：通过命令行同步**<small>（进阶）</small>

```bash
# 从主项目（neko）拉取更新
git checkout main
git pull neko main

# 推送到你的 Fork（origin）
git push origin main
```

### Q: 如何在提交 PR 前更新我的功能分支？<small>（进阶）</small>

如果你的功能分支需要包含主项目的最新更新：

```bash
# 确保主分支是最新的
# 先在 GitHub 上同步 Fork，然后：
git checkout main
git pull origin main

# 切换回功能分支
git checkout feature-branch

# 合并主分支的更新
git merge main

# 推送更新后的分支
git push origin feature-branch
```

**使用 rebase（更进阶，可选）：**

```bash
# 使用 rebase 可以让提交历史更整洁
git rebase main

# rebase 后需要强制推送（谨慎使用）
git push -f origin feature-branch
```

**注意**：强制推送（`-f`）会重写历史，只在你的个人分支上使用，不要在共享分支上使用。新手建议使用 `git merge` 方法。

---

## 下一步

掌握 Git 基础后，继续学习：

- [项目架构与目录结构](./03-project-structure.md) - 了解 Project N.E.K.O. 项目的组织方式
- [开发实战入门](./04-development-basics.md) - 开始你的第一次开发

---

**遇到问题？** 在 [GitHub Issues](https://github.com/Project-N-E-K-O/nekodemy/issues) 提交你的问题，我们会及时解答！

