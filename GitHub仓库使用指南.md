# GitHub 仓库使用完整指南

## 📚 目录
1. [创建新仓库](#创建新仓库)
2. [本地项目与GitHub连接](#本地项目与github连接)
3. [克隆和拉取特定分支](#克隆和拉取特定分支)
4. [基本Git操作](#基本git操作)
5. [分支管理](#分支管理)
6. [Pull Request (PR)](#pull-request-pr)
7. [Issues管理](#issues管理)
8. [仓库设置与管理](#仓库设置与管理)
9. [协作功能](#协作功能)
10. [常用命令速查](#常用命令速查)

---

## 创建新仓库

### 方法一：在GitHub网站上创建

1. **登录GitHub**
   - 访问 [github.com](https://github.com)
   - 登录你的账号

2. **创建新仓库**
   - 点击右上角的 **"+"** 图标
   - 选择 **"New repository"**（新建仓库）

3. **填写仓库信息**
   - **Repository name**（仓库名称）：输入项目名称，如 `my-project`
   - **Description**（描述）：可选，简要描述项目
   - **Visibility**（可见性）：
     - **Public**（公开）：所有人都能看到
     - **Private**（私有）：只有你和你邀请的人能看到
   - **Initialize this repository with**（初始化选项）：
     - ✅ Add a README file（添加README文件）
     - ✅ Add .gitignore（添加.gitignore文件，选择项目类型）
     - ✅ Choose a license（选择许可证，如MIT License）

4. **点击 "Create repository"** 完成创建

### 方法二：从本地项目创建

如果你已经有一个本地项目，可以将其推送到GitHub：

```bash
# 1. 在本地项目目录初始化Git
git init

# 2. 添加所有文件到暂存区
git add .

# 3. 提交文件
git commit -m "Initial commit"

# 4. 在GitHub上创建新仓库（不初始化README），然后执行：
git remote add origin https://github.com/你的用户名/仓库名.git
git branch -M main
git push -u origin main
```

---

## 本地项目与GitHub连接

### 首次连接本地项目到GitHub

```bash
# 1. 进入项目目录
cd /path/to/your/project

# 2. 初始化Git仓库（如果还没有）
git init

# 3. 添加远程仓库地址
git remote add origin https://github.com/你的用户名/仓库名.git

# 4. 查看远程仓库（确认添加成功）
git remote -v

# 5. 添加文件到暂存区
git add .

# 6. 提交到本地仓库
git commit -m "首次提交"

# 7. 推送到GitHub（首次推送）
git push -u origin main
```

### 使用SSH密钥（推荐，更安全）

1. **生成SSH密钥**
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# 按回车使用默认路径，设置密码（可选）
```

2. **添加SSH密钥到GitHub**
```bash
# 复制公钥内容
cat ~/.ssh/id_ed25519.pub
# 或 macOS 使用：
pbcopy < ~/.ssh/id_ed25519.pub
```

3. **在GitHub上添加SSH密钥**
   - 点击右上角头像 → **Settings**
   - 左侧菜单选择 **SSH and GPG keys**
   - 点击 **New SSH key**
   - 粘贴公钥内容，保存

4. **使用SSH地址连接**
```bash
git remote set-url origin git@github.com:你的用户名/仓库名.git
```

---

## 克隆和拉取特定分支

### 场景说明

当你需要克隆一个项目，但目标代码在某个特定分支上时，有以下几种方法：

### 方法一：克隆后切换到特定分支（推荐）

这是最常用的方法，先克隆整个仓库，然后切换到目标分支：

```bash
# 1. 克隆整个仓库（默认会克隆主分支，通常是main或master）
git clone https://github.com/用户名/仓库名.git

# 2. 进入项目目录
cd 仓库名

# 3. 查看所有分支（包括远程分支）
git branch -a

# 4. 切换到目标分支
git checkout 分支名
# 或使用新语法
git switch 分支名

# 如果分支在远程但本地不存在，会自动创建跟踪分支
git checkout -b 分支名 origin/分支名
```

**示例：**
```bash
# 克隆项目
git clone https://github.com/facebook/react.git
cd react

# 查看所有分支
git branch -a
# 输出示例：
# * main
#   remotes/origin/main
#   remotes/origin/18.0-stable
#   remotes/origin/17.0-stable

# 切换到18.0-stable分支
git checkout 18.0-stable
```

### 方法二：直接克隆特定分支（节省空间）

如果你只需要某个分支的代码，可以只克隆该分支：

```bash
# 克隆特定分支（只下载该分支的代码）
git clone -b 分支名 --single-branch https://github.com/用户名/仓库名.git

# 或者指定深度，只获取最新提交（更节省空间）
git clone -b 分支名 --single-branch --depth 1 https://github.com/用户名/仓库名.git
```

**参数说明：**
- `-b 分支名`：指定要克隆的分支
- `--single-branch`：只克隆指定的分支，不下载其他分支
- `--depth 1`：浅克隆，只获取最新一次提交（可选，节省空间）

**示例：**
```bash
# 只克隆develop分支
git clone -b develop --single-branch https://github.com/用户名/仓库名.git

# 只克隆最新提交的develop分支（节省空间）
git clone -b develop --single-branch --depth 1 https://github.com/用户名/仓库名.git
```

### 方法三：克隆后拉取特定分支

如果已经克隆了仓库，但需要切换到其他分支：

```bash
# 1. 查看远程分支
git branch -r

# 2. 获取远程分支信息
git fetch origin

# 3. 切换到远程分支（会自动创建本地跟踪分支）
git checkout 分支名
# 或
git switch 分支名

# 如果分支名与远程分支名不同，可以明确指定
git checkout -b 本地分支名 origin/远程分支名
```

### 方法四：在GitHub网站上查看分支

1. **查看所有分支**
   - 进入仓库页面
   - 点击分支下拉菜单（显示当前分支名的地方）
   - 可以看到所有分支列表

2. **切换到分支视图**
   - 在分支下拉菜单中选择目标分支
   - 页面会切换到该分支的代码视图

3. **获取分支的克隆地址**
   - 切换到目标分支后
   - 点击绿色的 **"Code"** 按钮
   - 复制HTTPS或SSH地址
   - 使用该地址克隆（克隆后会自动在该分支上）

### 拉取特定分支的最新更新

如果你已经在某个分支上，想拉取最新代码：

```bash
# 1. 确保在目标分支上
git checkout 分支名

# 2. 拉取该分支的最新代码
git pull origin 分支名

# 或者先fetch再merge
git fetch origin 分支名
git merge origin/分支名
```

### 查看远程分支信息

```bash
# 查看所有远程分支
git branch -r

# 查看所有分支（本地+远程）
git branch -a

# 查看远程仓库信息
git remote show origin
```

### 常见场景示例

#### 场景1：克隆项目并切换到开发分支

```bash
# 克隆项目
git clone https://github.com/用户名/仓库名.git
cd 仓库名

# 切换到develop分支
git checkout develop

# 拉取最新代码
git pull
```

#### 场景2：只获取某个功能分支的代码

```bash
# 直接克隆功能分支
git clone -b feature/user-login --single-branch https://github.com/用户名/仓库名.git
```

#### 场景3：在已有仓库中切换到其他分支

```bash
# 进入已克隆的仓库
cd 仓库名

# 获取所有远程分支信息
git fetch origin

# 切换到目标分支
git checkout feature/new-feature

# 如果本地没有该分支，创建并跟踪远程分支
git checkout -b feature/new-feature origin/feature/new-feature
```

#### 场景4：查看分支上的文件差异

```bash
# 查看两个分支的差异
git diff main..分支名

# 查看某个文件在不同分支的差异
git diff main..分支名 -- 文件路径
```

### 注意事项

1. **首次克隆时**：默认会克隆主分支（main或master），但所有分支信息都会下载
2. **切换分支前**：确保当前工作区是干净的（没有未提交的修改），否则需要先提交或暂存
3. **远程分支不存在**：如果分支名输入错误，Git会提示找不到该分支
4. **权限问题**：如果是私有仓库，确保你有访问权限

### 快速检查当前分支

```bash
# 查看当前所在分支
git branch

# 查看当前分支的详细信息
git status

# 查看所有分支及当前分支（带*号）
git branch -a
```

---

## 基本Git操作

### 日常开发流程

```bash
# 1. 查看当前状态
git status

# 2. 查看修改内容
git diff

# 3. 添加文件到暂存区
git add .                    # 添加所有文件
git add 文件名               # 添加特定文件
git add *.js                 # 添加所有.js文件

# 4. 提交更改
git commit -m "描述你的更改"

# 5. 推送到GitHub
git push

# 6. 从GitHub拉取最新代码
git pull
```

### 查看历史记录

```bash
# 查看提交历史
git log

# 简洁版历史
git log --oneline

# 图形化显示
git log --graph --oneline --all
```

### 撤销操作

```bash
# 撤销工作区的修改（未add的文件）
git checkout -- 文件名

# 撤销暂存区的文件（已add但未commit）
git reset HEAD 文件名

# 修改最后一次提交信息
git commit --amend -m "新的提交信息"

# 撤销最后一次提交（保留文件修改）
git reset --soft HEAD~1
```

---

## 分支管理

### 创建和切换分支

```bash
# 查看所有分支
git branch

# 创建新分支
git branch 分支名

# 切换到分支
git checkout 分支名

# 创建并切换到新分支（一步完成）
git checkout -b 分支名

# 或使用新语法
git switch -c 分支名
```

### 合并分支

```bash
# 1. 切换到主分支
git checkout main

# 2. 拉取最新代码
git pull

# 3. 合并功能分支
git merge 分支名

# 4. 推送合并后的代码
git push

# 5. 删除本地分支（可选）
git branch -d 分支名

# 6. 删除远程分支（可选）
git push origin --delete 分支名
```

### 分支命名规范

- `main` 或 `master`：主分支，生产环境代码
- `develop`：开发分支
- `feature/功能名`：功能分支，如 `feature/user-login`
- `bugfix/问题名`：修复分支，如 `bugfix/login-error`
- `hotfix/紧急修复`：紧急修复分支

---

## Pull Request (PR)

### 创建Pull Request

1. **推送分支到GitHub**
```bash
git push origin 分支名
```

2. **在GitHub上创建PR**
   - 进入仓库页面
   - 点击 **"Compare & pull request"** 按钮
   - 填写PR信息：
     - **Title**：PR标题
     - **Description**：详细描述更改内容
   - 选择审查者（Reviewers）
   - 点击 **"Create pull request"**

3. **PR审查流程**
   - 等待审查者审查
   - 根据反馈修改代码
   - 审查通过后合并到主分支

### 更新PR

如果PR需要修改，继续提交代码即可：

```bash
# 修改代码后
git add .
git commit -m "修复PR中的问题"
git push
# PR会自动更新
```

---

## Issues管理

### 创建Issue

1. 进入仓库页面
2. 点击 **"Issues"** 标签
3. 点击 **"New issue"**
4. 填写：
   - **Title**：问题标题
   - **Description**：详细描述
   - **Labels**：标签（bug, enhancement等）
   - **Assignees**：分配给谁
   - **Projects**：关联项目（可选）

### Issue标签

- `bug`：错误报告
- `enhancement`：功能建议
- `question`：问题咨询
- `documentation`：文档相关
- `good first issue`：适合新手的任务

### 关闭Issue

- 在提交信息中使用关键词自动关闭：
```bash
git commit -m "修复登录问题，关闭 #123"
# 或
git commit -m "Fixes #123"
```

关键词：`fixes`, `closes`, `resolves`, `fix`, `close`, `resolve`

---

## 仓库设置与管理

### 仓库设置

1. **进入设置**
   - 仓库页面 → **Settings**

2. **常用设置项**
   - **General**：
     - 修改仓库名称
     - 修改描述
     - 更改可见性（Public/Private）
     - 删除仓库
   
   - **Branches**：
     - 设置默认分支
     - 添加分支保护规则
   
   - **Collaborators**：
     - 添加协作者
     - 设置权限级别

### 添加协作者

1. 进入 **Settings** → **Collaborators**
2. 点击 **"Add people"**
3. 输入用户名或邮箱
4. 选择权限：
   - **Read**：只能查看
   - **Write**：可以推送代码
   - **Admin**：完全管理权限

### 分支保护规则

1. **Settings** → **Branches**
2. 点击 **"Add rule"**
3. 设置规则：
   - 分支名称模式（如 `main`）
   - ✅ Require pull request reviews（需要PR审查）
   - ✅ Require approvals（需要批准数量）
   - ✅ Require status checks（需要状态检查）

### 删除仓库

⚠️ **警告：删除仓库是不可逆的操作，请谨慎操作！**

#### 详细步骤

1. **进入仓库设置**
   - 打开要删除的仓库页面
   - 点击顶部的 **"Settings"** 标签

2. **找到删除选项**
   - 在设置页面中，向下滚动到最底部
   - 找到 **"Danger Zone"**（危险区域）部分
   - 点击 **"Delete this repository"**（删除此仓库）按钮

3. **确认删除**
   - 会弹出确认对话框
   - **必须输入仓库的完整名称**才能删除（例如：`my-project`）
   - 仔细阅读警告信息：
     - 此操作无法撤销
     - 将永久删除所有代码、Issues、Pull Requests等
     - 删除后无法恢复

4. **完成删除**
   - 输入仓库名称后，点击红色的 **"I understand the consequences, delete this repository"**（我了解后果，删除此仓库）按钮
   - 仓库将被永久删除

#### 删除前的准备工作

在删除仓库之前，建议：

1. **备份重要代码**
   ```bash
   # 克隆仓库到本地备份
   git clone https://github.com/你的用户名/仓库名.git
   ```

2. **导出Issues和Pull Requests**
   - 如果需要保留Issues和PR的历史记录
   - 可以在删除前手动复制重要信息

3. **通知协作者**
   - 如果仓库有协作者，提前通知他们
   - 让他们知道仓库将被删除

4. **检查依赖关系**
   - 确认没有其他项目依赖此仓库
   - 检查是否有Fork的仓库需要处理

#### 删除后的影响

- ✅ **本地代码不受影响**：删除GitHub上的仓库不会影响你本地的代码
- ❌ **远程仓库消失**：GitHub上的仓库、Issues、PR、Wiki等都会被删除
- ❌ **无法恢复**：删除后无法恢复，需要重新创建
- ❌ **链接失效**：所有指向该仓库的链接将失效

#### 替代方案

如果只是想隐藏仓库而不是删除：

1. **改为私有仓库**
   - Settings → General → Change visibility → Make private
   - 这样仓库仍然存在，只是不公开

2. **归档仓库**
   - Settings → General → Archive this repository
   - 仓库会被标记为只读，但不会被删除

#### 常见问题

**Q: 删除仓库后，本地代码还在吗？**
A: 是的，删除GitHub上的仓库不会影响本地代码。你仍然可以在本地继续开发，只是无法推送到GitHub。

**Q: 删除后可以恢复吗？**
A: 不可以。GitHub的删除操作是永久性的，无法恢复。建议删除前做好备份。

**Q: 如何删除Fork的仓库？**
A: 删除Fork的仓库步骤相同，但不会影响原始仓库。

**Q: 删除仓库需要什么权限？**
A: 只有仓库的所有者（Owner）或具有管理员权限的用户可以删除仓库。

---

## 协作功能

### Fork（分叉）

1. **Fork他人仓库**
   - 进入他人仓库页面
   - 点击右上角 **"Fork"**
   - 选择Fork到你的账号

2. **同步Fork的仓库**
```bash
# 添加上游仓库
git remote add upstream https://github.com/原作者/仓库名.git

# 获取上游更新
git fetch upstream

# 合并到你的分支
git merge upstream/main
```

### Star（星标）

- 点击仓库右上角的 ⭐ 按钮
- 用于收藏喜欢的项目

### Watch（关注）

- 点击仓库右上角的 **"Watch"**
- 选择通知级别：
  - **Not watching**：不接收通知
  - **Watching**：接收所有通知
  - **Releases only**：仅发布通知

---

## 常用命令速查

### 基础命令

```bash
# 初始化仓库
git init

# 克隆仓库
git clone https://github.com/用户名/仓库名.git
git clone git@github.com:用户名/仓库名.git  # SSH方式

# 查看状态
git status

# 查看差异
git diff

# 添加文件
git add .
git add 文件名

# 提交
git commit -m "提交信息"

# 推送
git push
git push origin 分支名

# 拉取
git pull
git pull origin 分支名

# 查看历史
git log
git log --oneline
```

### 分支命令

```bash
# 查看分支
git branch
git branch -a  # 包括远程分支

# 创建分支
git branch 分支名
git checkout -b 分支名

# 切换分支
git checkout 分支名
git switch 分支名

# 删除分支
git branch -d 分支名
git branch -D 分支名  # 强制删除
```

### 远程仓库命令

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin URL

# 修改远程仓库地址
git remote set-url origin URL

# 删除远程仓库
git remote remove origin

# 获取远程更新（不合并）
git fetch

# 拉取并合并
git pull
```

### 撤销命令

```bash
# 撤销工作区修改
git checkout -- 文件名
git restore 文件名

# 撤销暂存区
git reset HEAD 文件名
git restore --staged 文件名

# 修改最后一次提交
git commit --amend -m "新信息"
```

---

## 最佳实践

### 提交信息规范

使用清晰、描述性的提交信息：

```bash
# 好的提交信息
git commit -m "添加用户登录功能"
git commit -m "修复登录页面的样式问题"
git commit -m "更新README文档"

# 避免
git commit -m "更新"
git commit -m "修复bug"
git commit -m "asdf"
```

### .gitignore文件

创建 `.gitignore` 文件，忽略不需要版本控制的文件：

```
# 依赖
node_modules/
vendor/

# 构建文件
dist/
build/
*.class

# 环境变量
.env
.env.local

# IDE
.idea/
.vscode/
*.swp

# 系统文件
.DS_Store
Thumbs.db
```

### README.md文件

为每个仓库创建清晰的README：

```markdown
# 项目名称

## 简介
项目简要描述

## 安装
```bash
npm install
```

## 使用方法
```bash
npm start
```

## 贡献
欢迎提交PR和Issue

## 许可证
MIT License
```

---

## 常见问题

### Q: 如何删除仓库？
A: 
1. 进入仓库页面 → 点击 **Settings**
2. 滚动到页面最下方的 **Danger Zone**（危险区域）
3. 点击 **Delete this repository**
4. 输入仓库完整名称确认删除
5. 点击红色确认按钮完成删除

⚠️ **注意**：删除操作不可逆，请提前备份重要代码！详细步骤请参考[删除仓库](#删除仓库)章节。

### Q: 如何重命名仓库？
A: Settings → General → Repository name → 修改名称

### Q: 如何下载单个文件？
A: 进入文件页面 → 点击 Raw → 右键保存

### Q: 如何回退到之前的版本？
A: 
```bash
git log  # 查看提交历史，找到commit hash
git reset --hard commit_hash  # 回退到指定版本
git push -f  # 强制推送（谨慎使用）
```

### Q: 如何查看两个版本的差异？
A: 
```bash
git diff commit1 commit2
git diff HEAD~1  # 与上一个版本比较
```

---

## 下一步学习

- **GitHub Actions**：自动化CI/CD
- **GitHub Pages**：免费托管静态网站
- **GitHub Releases**：发布版本
- **GitHub Wiki**：项目文档
- **GitHub Discussions**：社区讨论

---

## 有用的资源

- [Git官方文档](https://git-scm.com/doc)
- [GitHub官方文档](https://docs.github.com)
- [Git教程 - 廖雪峰](https://www.liaoxuefeng.com/wiki/896043488029600)
- [GitHub Guides](https://guides.github.com)

---

**祝你使用GitHub愉快！** 🎉

如有问题，欢迎在仓库中创建Issue讨论。

