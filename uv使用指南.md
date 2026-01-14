# uv 完整使用指南

`uv` 是一个由 Astral 开发的高性能 Python 包管理器和项目管理工具，使用 Rust 编写，旨在提供比传统工具（如 pip、poetry）更快的依赖解析和安装体验。本指南将全面介绍 uv 的所有功能和使用方法。

---

## 目录

1. [安装与配置](#1-安装与配置)
2. [Python 版本管理](#2-python-版本管理)
3. [项目管理](#3-项目管理)
4. [依赖管理](#4-依赖管理)
5. [虚拟环境管理](#5-虚拟环境管理)
6. [脚本管理](#6-脚本管理)
7. [工具管理](#7-工具管理)
8. [构建与发布](#8-构建与发布)
9. [缓存管理](#9-缓存管理)
10. [pip 兼容接口](#10-pip-兼容接口)
11. [高级功能](#11-高级功能)
12. [最佳实践](#12-最佳实践)
13. [故障排除](#13-故障排除)

---

## 1. 安装与配置

### 1.1 系统要求

- **操作系统**：Windows 10+、macOS 10.15+、Linux (glibc 2.31+)
- **Python 版本**：无需预先安装 Python，`uv` 自带 Python 环境管理功能
- **架构支持**：x86_64、ARM64

### 1.2 安装方法

#### 方法一：官方安装脚本（推荐）

**Linux/macOS：**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows (PowerShell)：**
```powershell
irm https://astral.sh/uv/install.ps1 | iex
```

安装完成后，重启终端或执行：
```bash
source $HOME/.cargo/env  # Linux/macOS
```

#### 方法二：通过 pipx 安装

```bash
pipx install uv
```

#### 方法三：通过 Homebrew (macOS)

```bash
brew install uv
```

#### 方法四：通过 Cargo 安装

```bash
cargo install uv
```

#### 方法五：通过 pip 安装

```bash
pip install uv
```

### 1.3 验证安装

```bash
uv --version
```

### 1.4 更新 uv

```bash
uv self update
```

### 1.5 配置镜像源

#### 临时使用镜像源（环境变量）

```bash
# 阿里云镜像源
export UV_INDEX_URL="https://mirrors.aliyun.com/pypi/simple/"

# 清华大学镜像源
export UV_INDEX_URL="https://pypi.tuna.tsinghua.edu.cn/simple/"

# 腾讯云镜像源
export UV_INDEX_URL="https://mirrors.cloud.tencent.com/pypi/simple/"

# 豆瓣镜像源
export UV_INDEX_URL="https://pypi.douban.com/simple/"
```

#### 永久配置镜像源

**配置文件位置：**
- **Linux/macOS**：`~/.config/uv/uv.toml`
- **Windows**：`%APPDATA%\uv\uv.toml`

**配置内容：**
```toml
[index]
# 阿里云镜像源
url = "https://mirrors.aliyun.com/pypi/simple/"

# 或使用清华大学镜像源
# url = "https://pypi.tuna.tsinghua.edu.cn/simple/"

# 或使用腾讯云镜像源
# url = "https://mirrors.cloud.tencent.com/pypi/simple/"
```

#### 配置多个索引源

```toml
[[index]]
name = "pypi"
url = "https://pypi.org/simple"
default = true

[[index]]
name = "aliyun"
url = "https://mirrors.aliyun.com/pypi/simple/"
```

### 1.6 其他配置选项

```toml
# 设置缓存目录
cache-dir = "/path/to/cache"

# 设置工具目录
tool-dir = "/path/to/tools"

# 设置 Python 安装目录
python-dir = "/path/to/python"

# 设置超时时间（秒）
timeout = 300

# 设置重试次数
retries = 3

# 设置并发数
concurrency = 10
```

---

## 2. Python 版本管理

`uv` 提供了强大的 Python 版本管理功能，可以自动下载和管理多个 Python 版本。

### 2.1 安装 Python 版本

```bash
# 安装最新稳定版本
uv python install

# 安装指定版本
uv python install 3.12
uv python install 3.11.5
uv python install 3.10.12

# 安装预发布版本
uv python install 3.13.0a1

# 安装特定架构版本（macOS）
uv python install 3.12 --arch arm64
uv python install 3.12 --arch x86_64
```

### 2.2 列出可用的 Python 版本

```bash
# 列出所有可用版本
uv python list

# 列出已安装的版本
uv python list --installed

# 列出特定版本的详细信息
uv python list 3.12
```

### 2.3 查找已安装的 Python 版本

```bash
# 查找所有已安装的版本
uv python find

# 查找特定版本
uv python find 3.12

# 查找系统 Python
uv python find --system
```

### 2.4 固定项目 Python 版本

```bash
# 固定为特定版本
uv python pin 3.12

# 固定为当前使用的版本
uv python pin

# 查看当前固定的版本
cat .python-version
```

### 2.5 卸载 Python 版本

```bash
# 卸载特定版本
uv python uninstall 3.11

# 卸载所有未使用的版本
uv python uninstall --unused
```

### 2.6 设置默认 Python 版本

```bash
# 设置全局默认版本
uv python pin 3.12 --global
```

### 2.7 查看 Python 安装目录

```bash
uv python dir
```

---

## 3. 项目管理

`uv` 支持创建和管理带有 `pyproject.toml` 的 Python 项目，类似于 Poetry 和 PDM。

### 3.1 创建新项目

```bash
# 在当前目录创建项目
uv init

# 在指定目录创建项目
uv init my-project

# 创建项目并指定包名
uv init --name my-package

# 创建应用项目（非库项目）
uv init --app

# 创建库项目（默认）
uv init --lib

# 创建项目并指定 Python 版本
uv init --python 3.12

# 创建项目但不初始化 Git
uv init --no-readme
```

### 3.2 项目结构

创建项目后会生成以下结构：

```
my-project/
├── pyproject.toml      # 项目配置文件
├── README.md           # 项目说明
├── .gitignore          # Git 忽略文件
└── src/                # 源代码目录（库项目）
    └── my_package/
        └── __init__.py
```

### 3.3 pyproject.toml 配置

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "A sample project"
readme = "README.md"
requires-python = ">=3.10"
dependencies = [
    "requests>=2.31.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
]
```

### 3.4 同步项目依赖

```bash
# 同步所有依赖到虚拟环境
uv sync

# 同步并安装开发依赖
uv sync --dev

# 同步并安装可选依赖组
uv sync --extra dev --extra test

# 同步但不安装包（仅更新锁文件）
uv sync --no-install

# 同步并重新解析依赖
uv sync --refresh
```

### 3.5 锁定依赖

```bash
# 生成或更新锁文件
uv lock

# 强制重新解析依赖
uv lock --refresh

# 更新所有依赖到最新版本
uv lock --upgrade

# 更新特定依赖
uv lock --upgrade-package requests

# 预览更新（不实际更新）
uv lock --upgrade-package requests --dry-run
```

### 3.6 查看依赖树

```bash
# 查看完整依赖树
uv tree

# 查看特定包的依赖
uv tree requests

# 以 JSON 格式输出
uv tree --json

# 显示反向依赖（哪些包依赖了指定包）
uv tree --reverse requests
```

### 3.7 在项目环境中运行命令

```bash
# 运行 Python 脚本
uv run python script.py

# 运行任意命令
uv run pytest
uv run black .
uv run my-script

# 运行并传递参数
uv run python -m pytest tests/ -v

# 运行交互式 Python
uv run python
```

### 3.8 项目信息

```bash
# 显示项目信息
uv project info

# 显示项目依赖
uv project dependencies
```

---

## 4. 依赖管理

### 4.1 添加依赖

```bash
# 添加生产依赖
uv add requests

# 添加开发依赖
uv add --dev pytest
uv add -d black

# 添加可选依赖组
uv add --optional dev pytest
uv add --optional test pytest-cov

# 添加指定版本
uv add "requests>=2.31.0"
uv add "requests==2.31.0"
uv add "requests~=2.31.0"

# 添加 Git 依赖
uv add git+https://github.com/user/repo.git

# 添加本地路径依赖
uv add ./local-package
uv add ../other-project

# 添加多个依赖
uv add requests flask fastapi

# 添加并指定 extras
uv add "requests[security]"
```

### 4.2 移除依赖

```bash
# 移除依赖
uv remove requests

# 移除开发依赖
uv remove --dev pytest

# 移除可选依赖
uv remove --optional dev pytest

# 移除多个依赖
uv remove requests flask
```

### 4.3 更新依赖

```bash
# 更新所有依赖到最新版本
uv lock --upgrade

# 更新特定依赖
uv lock --upgrade-package requests

# 更新多个依赖
uv lock --upgrade-package requests --upgrade-package flask

# 预览更新
uv lock --upgrade-package requests --dry-run
```

### 4.4 依赖版本约束

```bash
# 精确版本
uv add "package==1.2.3"

# 兼容版本（~=）
uv add "package~=1.2.0"  # >=1.2.0, <1.3.0

# 大于等于
uv add "package>=1.2.0"

# 小于等于
uv add "package<=1.2.0"

# 范围版本
uv add "package>=1.2.0,<2.0.0"

# 通配符版本
uv add "package==1.2.*"
```

### 4.5 依赖组管理

```toml
[project.optional-dependencies]
dev = ["pytest", "black"]
test = ["pytest-cov", "pytest-mock"]
docs = ["sphinx", "sphinx-rtd-theme"]
```

```bash
# 安装特定依赖组
uv sync --extra dev --extra test

# 添加依赖到组
uv add --optional dev pytest

# 移除组中的依赖
uv remove --optional dev pytest
```

---

## 5. 虚拟环境管理

### 5.1 创建虚拟环境

```bash
# 在当前目录创建虚拟环境
uv venv

# 在指定目录创建
uv venv .venv
uv venv /path/to/venv

# 使用特定 Python 版本
uv venv --python 3.12
uv venv --python python3.11

# 创建系统站点包环境
uv venv --system-site-packages

# 创建不包含 pip 的环境
uv venv --without-pip

# 创建符号链接环境（节省空间）
uv venv --symlinks
```

### 5.2 激活虚拟环境

**Linux/macOS：**
```bash
source .venv/bin/activate
```

**Windows：**
```powershell
.venv\Scripts\activate
```

### 5.3 使用虚拟环境（无需激活）

```bash
# 直接使用虚拟环境中的 Python
.venv/bin/python script.py  # Linux/macOS
.venv\Scripts\python script.py  # Windows

# 使用 uv run（自动使用项目虚拟环境）
uv run python script.py
```

### 5.4 删除虚拟环境

```bash
# 直接删除目录
rm -rf .venv  # Linux/macOS
rmdir /s .venv  # Windows
```

### 5.5 列出虚拟环境

```bash
# uv 不直接提供此功能，但可以通过查找 .venv 目录实现
find . -name ".venv" -type d
```

---

## 6. 脚本管理

`uv` 允许您执行独立的 Python 脚本，并自动管理其依赖。

### 6.1 运行脚本

```bash
# 运行脚本（自动创建临时环境）
uv run script.py

# 运行脚本并传递参数
uv run script.py --arg1 value1

# 运行脚本并指定 Python 版本
uv run --python 3.12 script.py
```

### 6.2 为脚本添加依赖

```bash
# 添加依赖到脚本
uv add --script requests

# 添加多个依赖
uv add --script requests beautifulsoup4

# 添加开发依赖
uv add --script --dev pytest
```

### 6.3 从脚本中移除依赖

```bash
# 移除依赖
uv remove --script requests
```

### 6.4 脚本依赖文件

脚本的依赖可以存储在 `pyproject.toml` 中：

```toml
[[tool.uv.script]]
name = "script.py"
dependencies = ["requests", "beautifulsoup4"]
```

---

## 7. 工具管理

`uv` 提供了专门的接口用于运行和安装发布到 Python 包索引的工具。

### 7.1 运行工具（临时环境）

```bash
# 运行工具（自动创建临时环境）
uvx ruff
uvx black .
uvx pytest

# uvx 是 uv tool run 的别名
uv tool run ruff

# 运行工具并传递参数
uvx black --check .
uvx pytest tests/ -v

# 运行特定版本的工具
uvx ruff@0.1.0

# 运行工具并指定 Python 版本
uvx --python 3.12 ruff
```

### 7.2 安装工具（全局）

```bash
# 安装工具到用户目录
uv tool install ruff

# 安装特定版本
uv tool install ruff@0.1.0

# 安装多个工具
uv tool install ruff black pytest

# 安装并添加到 PATH
uv tool install ruff --add-to-path
```

### 7.3 卸载工具

```bash
# 卸载工具
uv tool uninstall ruff

# 卸载多个工具
uv tool uninstall ruff black
```

### 7.4 列出已安装的工具

```bash
# 列出所有已安装的工具
uv tool list

# 列出工具的详细信息
uv tool list --verbose
```

### 7.5 更新工具

```bash
# 更新工具到最新版本
uv tool update ruff

# 更新所有工具
uv tool update --all
```

### 7.6 更新 Shell 配置

```bash
# 更新 shell 配置以包含工具目录
uv tool update-shell

# 指定 shell 类型
uv tool update-shell --shell bash
uv tool update-shell --shell zsh
uv tool update-shell --shell fish
```

### 7.7 查看工具目录

```bash
# 显示工具安装目录
uv tool dir
```

### 7.8 常用工具示例

```bash
# 代码格式化
uvx black .
uvx isort .

# 代码检查
uvx ruff check .
uvx pylint script.py

# 类型检查
uvx mypy .
uvx pyright .

# 测试
uvx pytest
uvx unittest

# 构建工具
uvx build
uvx twine upload dist/*
```

---

## 8. 构建与发布

### 8.1 构建项目

```bash
# 构建项目（生成分发包）
uv build

# 构建并指定输出目录
uv build --out-dir dist/

# 构建特定格式
uv build --wheel
uv build --sdist

# 构建并显示详细信息
uv build --verbose
```

### 8.2 发布项目

```bash
# 发布到 PyPI
uv publish

# 发布到测试 PyPI
uv publish --repository testpypi

# 发布并跳过现有文件
uv publish --skip-existing

# 发布并显示详细信息
uv publish --verbose

# 发布到自定义仓库
uv publish --repository-url https://custom-pypi.com/simple/
```

### 8.3 发布配置

在 `pyproject.toml` 中配置发布信息：

```toml
[project]
name = "my-package"
version = "0.1.0"
description = "My package description"
authors = [
    {name = "Your Name", email = "your.email@example.com"}
]
readme = "README.md"
license = {text = "MIT"}
keywords = ["package", "example"]
classifiers = [
    "Development Status :: 4 - Beta",
    "Programming Language :: Python :: 3",
]
```

### 8.4 发布凭证

```bash
# 使用 API token（推荐）
export UV_PUBLISH_TOKEN="pypi-xxxxxxxxxxxxx"

# 或使用用户名和密码
export UV_PUBLISH_USERNAME="username"
export UV_PUBLISH_PASSWORD="password"
```

---

## 9. 缓存管理

`uv` 使用缓存来加速包下载和安装。

### 9.1 查看缓存信息

```bash
# 显示缓存目录路径
uv cache dir

# 显示缓存统计信息
uv cache info
```

### 9.2 清理缓存

```bash
# 清理所有缓存
uv cache clean

# 清理过期的缓存条目
uv cache prune

# 清理特定包的缓存
uv cache clean requests

# 清理并显示详细信息
uv cache clean --verbose
```

### 9.3 缓存配置

```toml
# 在 uv.toml 中配置缓存目录
cache-dir = "/path/to/cache"

# 设置缓存大小限制
cache-size-limit = "10GB"
```

---

## 10. pip 兼容接口

`uv` 提供了与 `pip` 类似的接口，方便手动管理环境和包。

### 10.1 创建虚拟环境

```bash
# 创建虚拟环境
uv venv

# 使用特定 Python 版本
uv venv --python 3.12
```

### 10.2 安装包

```bash
# 安装包到当前环境
uv pip install requests

# 安装多个包
uv pip install requests flask fastapi

# 安装指定版本
uv pip install "requests==2.31.0"

# 安装开发版本
uv pip install git+https://github.com/user/repo.git

# 安装本地包
uv pip install ./local-package

# 从 requirements.txt 安装
uv pip install -r requirements.txt

# 安装到用户目录
uv pip install --user requests

# 升级包
uv pip install --upgrade requests

# 强制重新安装
uv pip install --force-reinstall requests
```

### 10.3 卸载包

```bash
# 卸载包
uv pip uninstall requests

# 卸载多个包
uv pip uninstall requests flask

# 卸载并确认
uv pip uninstall -y requests
```

### 10.4 列出包

```bash
# 列出所有已安装的包
uv pip list

# 列出过时的包
uv pip list --outdated

# 列出可升级的包
uv pip list --upgradable

# 列出特定包
uv pip list requests
```

### 10.5 显示包信息

```bash
# 显示包的详细信息
uv pip show requests

# 显示所有信息
uv pip show --verbose requests
```

### 10.6 冻结依赖

```bash
# 生成 requirements.txt
uv pip freeze > requirements.txt

# 生成包含哈希的 requirements.txt
uv pip freeze --hash > requirements.txt

# 生成特定格式
uv pip freeze --format requirements-txt
```

### 10.7 检查依赖

```bash
# 检查依赖兼容性
uv pip check

# 检查并显示详细信息
uv pip check --verbose
```

### 10.8 查看依赖树

```bash
# 显示依赖树
uv pip tree

# 显示特定包的依赖
uv pip tree requests

# 显示反向依赖
uv pip tree --reverse requests

# 以 JSON 格式输出
uv pip tree --json
```

### 10.9 编译依赖

```bash
# 编译 requirements.txt 为锁定文件
uv pip compile requirements.txt

# 编译并输出到文件
uv pip compile requirements.txt -o requirements.lock

# 升级所有依赖
uv pip compile --upgrade requirements.txt

# 升级特定依赖
uv pip compile --upgrade-package requests requirements.txt

# 预览升级
uv pip compile --upgrade-package requests --dry-run requirements.txt
```

### 10.10 同步环境

```bash
# 根据锁定文件同步环境
uv pip sync requirements.lock

# 同步并显示详细信息
uv pip sync --verbose requirements.lock

# 同步并重新安装
uv pip sync --reinstall requirements.lock
```

### 10.11 搜索包

```bash
# 搜索包（如果支持）
uv pip search requests
```

---

## 11. 高级功能

### 11.1 工作区（Workspace）

管理多个相关项目：

```toml
# 在根目录的 pyproject.toml 中
[tool.uv.workspace]
members = ["project1", "project2", "project3"]
```

```bash
# 在工作区中同步所有项目
uv sync --workspace

# 在工作区中运行命令
uv run --workspace pytest
```

### 11.2 环境变量

```bash
# 设置索引 URL
export UV_INDEX_URL="https://pypi.org/simple"

# 设置超时时间
export UV_TIMEOUT=300

# 设置重试次数
export UV_RETRIES=3

# 设置并发数
export UV_CONCURRENCY=10

# 禁用缓存
export UV_NO_CACHE=1

# 设置详细输出
export UV_VERBOSE=1
```

### 11.3 配置文件优先级

1. 命令行参数
2. 环境变量
3. 项目 `pyproject.toml` 中的 `[tool.uv]`
4. 用户配置文件 `~/.config/uv/uv.toml`
5. 系统默认值

### 11.4 自定义构建后端

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

# 或使用其他后端
# requires = ["setuptools", "wheel"]
# build-backend = "setuptools.build_meta"
```

### 11.5 脚本定义

在 `pyproject.toml` 中定义可执行脚本：

```toml
[project.scripts]
my-command = "my_package.cli:main"

[project.gui-scripts]
my-gui = "my_package.gui:main"
```

### 11.6 入口点

```toml
[project.entry-points."console_scripts"]
my-script = "my_package.cli:main"

[project.entry-points."custom.group"]
custom-entry = "my_package.custom:function"
```

### 11.7 平台特定依赖

```toml
[project]
dependencies = [
    "requests",
    {marker = "sys_platform == 'win32'", dependency = "pywin32"},
    {marker = "sys_platform == 'darwin'", dependency = "pyobjc"},
]
```

### 11.8 源分发和轮子

```bash
# 仅构建源分发
uv build --sdist

# 仅构建轮子
uv build --wheel

# 构建通用轮子
uv build --universal
```

### 11.9 并行安装

```bash
# 设置并行安装数
uv pip install --jobs 4 requests

# 或通过环境变量
export UV_JOBS=4
```

### 11.10 离线模式

```bash
# 仅使用缓存，不下载新包
uv pip install --offline requests

# 或
export UV_OFFLINE=1
```

---

## 12. 最佳实践

### 12.1 项目初始化

```bash
# 1. 创建项目
uv init my-project
cd my-project

# 2. 固定 Python 版本
uv python pin 3.12

# 3. 添加依赖
uv add requests flask

# 4. 添加开发依赖
uv add --dev pytest black ruff

# 5. 同步环境
uv sync
```

### 12.2 依赖管理

1. **使用版本约束**：始终指定版本范围
   ```bash
   uv add "requests>=2.31.0,<3.0.0"
   ```

2. **定期更新依赖**：
   ```bash
   uv lock --upgrade --dry-run  # 预览
   uv lock --upgrade            # 实际更新
   ```

3. **使用依赖组**：将开发、测试、文档依赖分组管理

4. **锁定依赖**：始终提交 `uv.lock` 文件到版本控制

### 12.3 虚拟环境

1. **使用项目虚拟环境**：让 `uv` 自动管理
2. **不要提交虚拟环境**：在 `.gitignore` 中添加 `.venv`
3. **使用 `uv run`**：无需手动激活环境

### 12.4 性能优化

1. **配置镜像源**：使用国内镜像加速下载
2. **定期清理缓存**：`uv cache prune`
3. **使用并行安装**：设置合适的并发数
4. **利用缓存**：不要频繁清理缓存

### 12.5 持续集成

```yaml
# GitHub Actions 示例
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: astral-sh/setup-uv@v1
      - run: uv sync
      - run: uv run pytest
```

### 12.6 多 Python 版本测试

```bash
# 测试多个 Python 版本
for version in 3.10 3.11 3.12; do
  uv python install $version
  uv venv --python $version
  uv sync
  uv run pytest
done
```

### 12.7 迁移现有项目

```bash
# 从 requirements.txt 迁移
uv pip compile requirements.txt -o uv.lock
uv sync

# 从 Poetry 迁移
# 1. 复制 pyproject.toml
# 2. 运行 uv sync
```

---

## 13. 故障排除

### 13.1 常见问题

#### 问题：安装速度慢

**解决方案：**
```bash
# 配置国内镜像源
export UV_INDEX_URL="https://mirrors.aliyun.com/pypi/simple/"
```

#### 问题：依赖解析失败

**解决方案：**
```bash
# 清理缓存并重新解析
uv cache clean
uv lock --refresh
```

#### 问题：Python 版本不匹配

**解决方案：**
```bash
# 固定 Python 版本
uv python pin 3.12

# 或指定版本创建环境
uv venv --python 3.12
```

#### 问题：权限错误

**解决方案：**
```bash
# 使用用户安装
uv pip install --user package

# 或检查目录权限
chmod -R u+w .venv
```

#### 问题：锁文件冲突

**解决方案：**
```bash
# 重新生成锁文件
rm uv.lock
uv lock
```

### 13.2 调试技巧

```bash
# 启用详细输出
uv --verbose sync

# 显示依赖解析过程
uv lock --verbose

# 检查环境状态
uv pip check

# 查看依赖树
uv tree
```

### 13.3 获取帮助

```bash
# 查看命令帮助
uv --help
uv add --help
uv sync --help

# 查看版本信息
uv --version

# 查看配置
uv cache dir
uv tool dir
uv python dir
```

### 13.4 报告问题

如果遇到 bug，可以：
1. 检查 [GitHub Issues](https://github.com/astral-sh/uv/issues)
2. 查看 [文档](https://docs.astral.sh/uv/)
3. 提交新的 Issue（包含版本信息和错误日志）

---

## 14. 命令速查表

### 14.1 项目管理

| 命令 | 说明 |
|------|------|
| `uv init` | 创建新项目 |
| `uv sync` | 同步项目依赖 |
| `uv lock` | 生成/更新锁文件 |
| `uv tree` | 查看依赖树 |
| `uv run <cmd>` | 在项目环境中运行命令 |
| `uv build` | 构建项目 |
| `uv publish` | 发布项目 |

### 14.2 依赖管理

| 命令 | 说明 |
|------|------|
| `uv add <pkg>` | 添加依赖 |
| `uv remove <pkg>` | 移除依赖 |
| `uv lock --upgrade` | 更新所有依赖 |
| `uv lock --upgrade-package <pkg>` | 更新特定依赖 |

### 14.3 Python 管理

| 命令 | 说明 |
|------|------|
| `uv python install <ver>` | 安装 Python 版本 |
| `uv python list` | 列出可用版本 |
| `uv python find` | 查找已安装版本 |
| `uv python pin <ver>` | 固定 Python 版本 |
| `uv python uninstall <ver>` | 卸载 Python 版本 |

### 14.4 工具管理

| 命令 | 说明 |
|------|------|
| `uvx <tool>` | 运行工具（临时） |
| `uv tool install <tool>` | 安装工具（全局） |
| `uv tool uninstall <tool>` | 卸载工具 |
| `uv tool list` | 列出已安装工具 |
| `uv tool update <tool>` | 更新工具 |

### 14.5 pip 接口

| 命令 | 说明 |
|------|------|
| `uv venv` | 创建虚拟环境 |
| `uv pip install <pkg>` | 安装包 |
| `uv pip uninstall <pkg>` | 卸载包 |
| `uv pip list` | 列出已安装包 |
| `uv pip freeze` | 冻结依赖 |
| `uv pip show <pkg>` | 显示包信息 |
| `uv pip check` | 检查依赖 |
| `uv pip tree` | 查看依赖树 |
| `uv pip compile` | 编译依赖 |
| `uv pip sync` | 同步环境 |

### 14.6 缓存管理

| 命令 | 说明 |
|------|------|
| `uv cache dir` | 显示缓存目录 |
| `uv cache clean` | 清理所有缓存 |
| `uv cache prune` | 清理过期缓存 |

### 14.7 系统管理

| 命令 | 说明 |
|------|------|
| `uv self update` | 更新 uv |
| `uv --version` | 显示版本 |
| `uv --help` | 显示帮助 |

---

## 15. 与其他工具对比

### 15.1 vs pip

| 特性 | pip | uv |
|------|-----|-----|
| 速度 | 慢 | 快（10-100x） |
| 依赖解析 | 基础 | 高级 |
| Python 管理 | 无 | 有 |
| 锁文件 | 无 | 有 |
| 项目管理 | 无 | 有 |

### 15.2 vs Poetry

| 特性 | Poetry | uv |
|------|--------|-----|
| 速度 | 中等 | 快 |
| 依赖解析 | 好 | 更好 |
| Python 管理 | 无 | 有 |
| 学习曲线 | 陡 | 平缓 |
| Rust 实现 | 否 | 是 |

### 15.3 vs PDM

| 特性 | PDM | uv |
|------|-----|-----|
| 速度 | 快 | 更快 |
| PEP 517/621 | 是 | 是 |
| Python 管理 | 有 | 有 |
| 社区 | 小 | 增长中 |

---

## 16. 总结

`uv` 是一个功能强大、性能卓越的 Python 包管理器和项目管理工具。它结合了 pip 的简单性、Poetry 的项目管理能力和 PDM 的速度，同时提供了独特的 Python 版本管理功能。

### 主要优势：

1. **极快的速度**：比传统工具快 10-100 倍
2. **完整的项目管理**：从创建到发布的完整工作流
3. **Python 版本管理**：无需手动安装 Python
4. **pip 兼容**：可以替代 pip 使用
5. **现代化设计**：基于 Rust，遵循最新 PEP 标准

### 适用场景：

- 新项目开发
- 现有项目迁移
- CI/CD 流水线
- 多 Python 版本测试
- 快速工具执行

通过本指南，您应该能够充分利用 `uv` 的所有功能，提升 Python 开发效率。如有问题，请参考官方文档或提交 Issue。

---

**参考资源：**

- 官方文档：https://docs.astral.sh/uv/
- GitHub 仓库：https://github.com/astral-sh/uv
- 问题反馈：https://github.com/astral-sh/uv/issues
