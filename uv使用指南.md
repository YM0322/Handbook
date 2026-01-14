# uv 完整使用指南

`uv` 是一个由 Astral 开发的高性能 Python 包管理器和项目管理工具，使用 Rust 编写，旨在提供比传统工具（如 pip、poetry）更快的依赖解析和安装体验。本指南将全面介绍 uv 的所有功能和使用方法。

---

## 目录

1. [安装与配置](#1-安装与配置)
2. [快速开始：实战流程](#2-快速开始实战流程)
3. [Python 版本管理](#3-python-版本管理)
4. [项目管理](#4-项目管理)
5. [依赖管理](#5-依赖管理)
6. [虚拟环境管理](#6-虚拟环境管理)
7. [脚本管理](#7-脚本管理)
8. [工具管理](#8-工具管理)
9. [构建与发布](#9-构建与发布)
10. [缓存管理](#10-缓存管理)
11. [pip 兼容接口](#11-pip-兼容接口)
12. [高级功能](#12-高级功能)
13. [最佳实践](#13-最佳实践)
14. [故障排除](#14-故障排除)

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

## 2. 快速开始：实战流程

本章节提供了一个完整的实战流程，帮助您快速上手使用 `uv` 管理 Python 项目。按照以下步骤操作，您将能够创建一个完整的 Python 项目并管理其依赖。

### 2.1 使用 uv 管理 Python 项目的完整流程

为确保 Python 程序可以正常运行，需要完成以下两个核心任务：
1. **确定 Python 版本**
2. **解决依赖问题**

#### 步骤一：确定 Python 版本

**1.1 查看所有可用的 Python 版本**

```bash
# 查看 uv 支持的所有 Python 版本
uv python list
```

**1.2 安装特定版本的 Python**

```bash
# 安装指定版本的 Python（注意：直接使用版本号，不需要 cpython- 前缀）
uv python install 3.12
uv python install 3.11.5
uv python install 3.10.12
```

**1.3 使用特定版本的 Python 运行脚本**

```bash
# 使用指定版本的 Python 临时运行一个文件
uv run --python 3.12 script.py

# 使用指定版本的 Python 进入交互式界面
uv run --python 3.12 python

# 使用 PyPy（如果之前没有下载过，会自动安装）
uv run --python pypy python
```

#### 步骤二：创建 uv 项目并解决依赖

**2.1 创建 uv 项目**

```bash
# 创建项目并指定 Python 版本
uv init -p 3.12

# 或创建项目时指定项目名称
uv init my-project -p 3.12
```

创建项目后会生成以下文件结构：

```
my-project/
├── hello.py              # 示例代码文件（可删除）
├── .python-version       # Python 版本信息文件（可直接修改以更换版本）
├── .venv/                # uv 自动创建的虚拟环境目录
├── pyproject.toml        # 项目配置文件（依赖记录在这里）
└── README.md             # 项目说明文件
```

**重要文件说明：**
- `.python-version`：记录了项目使用的 Python 版本，如需更换版本，可直接修改此文件
- `pyproject.toml`：项目的核心配置文件，所有依赖都会被记录在这里
- `.venv`：项目的虚拟环境目录，包含所有已安装的包

**2.2 查看项目依赖关系**

```bash
# 查看工程内所有库之间的依赖关系树
uv tree

# 查看特定包的依赖关系
uv tree requests
```

**2.3 创建并激活虚拟环境（可选）**

如果您需要创建独立的虚拟环境（而不是使用项目自动创建的 `.venv`）：

```bash
# 创建名为 ym_study 的虚拟环境
uv venv ym_study

# 激活虚拟环境（macOS/Linux）
source ym_study/bin/activate

# 激活虚拟环境（Windows PowerShell）
ym_study\Scripts\activate

# 激活虚拟环境（Windows CMD）
ym_study\Scripts\activate.bat
```

**2.4 从 requirements.txt 安装依赖**

如果项目中有 `requirements.txt` 文件，可以使用以下命令安装：

```bash
# 在激活的虚拟环境中安装依赖
uv pip install -r requirements.txt

# 或直接使用项目环境（无需激活）
uv run pip install -r requirements.txt
```

**2.5 添加项目依赖**

当代码中提示找不到某些库时，可以使用以下命令安装：

```bash
# 添加依赖到项目（会自动安装并更新 pyproject.toml）
uv add requests

# 添加多个依赖
uv add requests flask fastapi

# 添加开发依赖（不会被打包）
uv add --dev pytest black ruff
```

**重要提示：**
- 使用 `uv add` 命令时，uv 不仅会下载库文件，还会自动创建或更新项目的虚拟环境（`.venv` 目录）
- 在 VSCode 中，点击右下角的 Python 解释器选择器，选择项目中的 `.venv` 虚拟环境，即可解决导入错误
- 添加依赖后，可以在终端使用 `uv run script.py` 运行脚本

**2.6 运行项目脚本**

```bash
# 使用项目环境运行 Python 脚本
uv run script.py

# 运行并传递参数
uv run script.py --arg1 value1

# 运行任意命令
uv run pytest
uv run python -m unittest
```

### 2.2 安装和管理工具

#### 方法一：作为项目依赖安装（推荐用于项目特定工具）

```bash
# 添加工具作为开发依赖（避免打包时包含）
uv add ruff --dev

# 从依赖中移除工具
uv remove ruff --dev
```

#### 方法二：作为全局工具安装（推荐用于通用工具）

```bash
# 安装工具到系统（整个系统可用）
uv tool install ruff

# 查看系统中所有已安装的工具
uv tool list

# 卸载工具
uv tool uninstall ruff

# 更新工具
uv tool update ruff
```

#### 方法三：临时运行工具（无需安装）

```bash
# 临时运行工具（自动创建临时环境）
uvx ruff check .
uvx black --check .
```

**工具使用示例：**

```bash
# 使用 ruff 检查代码规范
uv add ruff --dev
uv run ruff check .

# 或使用全局安装的工具
uv tool install ruff
ruff check .

# 或临时运行
uvx ruff check .
```

### 2.3 打包和发布项目

#### 步骤一：配置项目脚本入口点

在 `pyproject.toml` 文件中添加 `[project.scripts]` 部分：

```toml
[project.scripts]
# 格式：命令名 = "模块路径:函数名"
ai = "ai:main"
my-cli = "my_package.cli:main"
hello = "hello:say_hello"
```

**示例：**

假设您有一个 `ai.py` 文件，其中包含 `main()` 函数：

```python
# ai.py
def main():
    print("Hello from AI!")

if __name__ == "__main__":
    main()
```

在 `pyproject.toml` 中配置：

```toml
[project.scripts]
ai = "ai:main"
```

配置后，安装项目后就可以直接使用 `ai` 命令了。

#### 步骤二：构建项目

```bash
# 构建项目为 whl 文件（轮子格式）
uv build

# 构建后会生成 dist/ 目录，包含构建好的分发包
```

#### 步骤三：使用打包的项目

构建完成后，可以通过以下方式使用：

```bash
# 方法一：使用 uv tool（全局安装）
uv tool install ./dist/my_project-0.1.0-py3-none-any.whl

# 方法二：作为依赖添加到其他项目
uv add ./dist/my_project-0.1.0-py3-none-any.whl

# 方法三：直接安装到当前环境
uv pip install ./dist/my_project-0.1.0-py3-none-any.whl
```

安装后，就可以使用配置的命令名（如 `ai`）来运行脚本了。

### 2.4 完整项目创建流程总结

创建一个新项目并使用 uv 管理的完整步骤如下：

**第一步：创建项目**

```bash
# 在终端中使用以下命令创建 uv 项目
uv init my-project -p 3.12
cd my-project
```

**第二步：添加依赖**

```bash
# 添加生产依赖
uv add requests flask

# 添加开发依赖
uv add --dev pytest black ruff
```

**第三步：编写代码**

创建您的源代码文件，例如 `main.py`：

```python
import requests

def main():
    response = requests.get("https://api.github.com")
    print(response.status_code)

if __name__ == "__main__":
    main()
```

**第四步：运行项目**

```bash
# 运行脚本
uv run main.py

# 或运行测试
uv run pytest
```

**第五步：配置脚本入口点（可选）**

在 `pyproject.toml` 中添加：

```toml
[project.scripts]
my-app = "main:main"
```

**第六步：构建和发布（可选）**

```bash
# 构建项目
uv build

# 发布到 PyPI（需要配置凭证）
uv publish
```

### 2.5 常见问题解决

**问题 1：VSCode 中导入库报错**

**解决方案：**
1. 确保已使用 `uv add` 安装了依赖
2. 点击 VSCode 右下角的 Python 解释器选择器
3. 选择项目中的 `.venv/bin/python`（macOS/Linux）或 `.venv\Scripts\python.exe`（Windows）

**问题 2：如何更换项目的 Python 版本**

**解决方案：**
1. 直接编辑 `.python-version` 文件，修改版本号
2. 或使用命令：`uv python pin 3.11`
3. 重新同步依赖：`uv sync`

**问题 3：如何从现有项目迁移到 uv**

**解决方案：**
1. 在项目目录运行：`uv init`
2. 如果有 `requirements.txt`，运行：`uv pip install -r requirements.txt`
3. 然后使用 `uv pip freeze` 生成新的依赖列表，或直接使用 `uv add` 逐个添加

---

## 3. Python 版本管理

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

## 4. 项目管理

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

## 5. 依赖管理

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

## 6. 虚拟环境管理

### 6.1 虚拟环境的存储位置

**默认存储位置：**

当您使用 `uv` 创建项目或添加依赖时，虚拟环境默认存储在**项目目录**下：

- **项目自动创建的虚拟环境**：`项目目录/.venv/`
- **手动创建的虚拟环境**：取决于您指定的路径

**示例：**

```bash
# 在项目 my-project 中
cd my-project
uv init -p 3.12
# 虚拟环境位置：my-project/.venv/

# 手动创建虚拟环境
uv venv my-venv
# 虚拟环境位置：my-project/my-venv/

# 在指定路径创建
uv venv ~/venvs/project1
# 虚拟环境位置：~/venvs/project1/
```

**重要提示：**
- 每个项目的虚拟环境默认是**独立的**，存储在各自的项目目录中
- 虚拟环境目录通常包含大量文件，建议将其添加到 `.gitignore` 中
- 如果删除项目目录，对应的虚拟环境也会被删除

### 6.2 查看虚拟环境位置

**方法一：查看当前项目的虚拟环境**

```bash
# 在项目目录中，虚拟环境通常在 .venv 目录
ls -la .venv  # Linux/macOS
dir .venv     # Windows

# 查看虚拟环境的 Python 路径
.venv/bin/python --version  # Linux/macOS
.venv\Scripts\python --version  # Windows
```

**方法二：查找所有项目中的虚拟环境**

```bash
# 在当前目录及子目录中查找所有 .venv 目录
find . -name ".venv" -type d

# 在指定目录中查找（例如主目录下的所有项目）
find ~/projects -name ".venv" -type d

# 查找所有虚拟环境目录（包括自定义名称）
find ~/projects -type d -name "venv" -o -name ".venv" -o -name "*env"

# Windows PowerShell 中查找
Get-ChildItem -Path . -Filter ".venv" -Recurse -Directory
```

**方法三：查看项目配置中的虚拟环境信息**

```bash
# 查看 pyproject.toml 中的配置
cat pyproject.toml | grep -A 5 "\[tool.uv\]"

# 或直接查看文件
cat pyproject.toml
```

**方法四：使用环境变量查看**

```bash
# 查看当前激活的虚拟环境
echo $VIRTUAL_ENV  # Linux/macOS
echo %VIRTUAL_ENV%  # Windows CMD
$env:VIRTUAL_ENV    # Windows PowerShell
```

### 6.3 统一管理所有项目的虚拟环境

如果您希望将所有项目的虚拟环境集中管理，可以采用以下方法：

**方法一：创建统一的虚拟环境目录**

```bash
# 1. 创建统一的虚拟环境存储目录
mkdir -p ~/.venvs  # Linux/macOS
mkdir %USERPROFILE%\.venvs  # Windows

# 2. 为每个项目在统一目录下创建虚拟环境
cd project1
uv venv ~/.venvs/project1

cd ../project2
uv venv ~/.venvs/project2

# 3. 激活时指定路径
source ~/.venvs/project1/bin/activate  # Linux/macOS
~/.venvs/project1/Scripts/activate     # Windows
```

**方法二：使用脚本管理所有虚拟环境**

创建一个管理脚本 `list-venvs.sh`（Linux/macOS）：

```bash
#!/bin/bash
# 列出所有找到的虚拟环境

echo "=== 项目虚拟环境列表 ==="
find ~/projects -name ".venv" -type d | while read venv; do
    project_dir=$(dirname "$venv")
    project_name=$(basename "$project_dir")
    python_version=$("$venv/bin/python" --version 2>&1)
    echo "项目: $project_name"
    echo "  路径: $venv"
    echo "  Python: $python_version"
    echo ""
done
```

创建 PowerShell 脚本 `list-venvs.ps1`（Windows）：

```powershell
# 列出所有找到的虚拟环境
Write-Host "=== 项目虚拟环境列表 ===" -ForegroundColor Green

Get-ChildItem -Path $HOME\projects -Filter ".venv" -Recurse -Directory | ForEach-Object {
    $projectDir = $_.Parent.FullName
    $projectName = $_.Parent.Name
    $pythonVersion = & "$($_.FullName)\Scripts\python.exe" --version 2>&1
    Write-Host "项目: $projectName" -ForegroundColor Yellow
    Write-Host "  路径: $($_.FullName)"
    Write-Host "  Python: $pythonVersion"
    Write-Host ""
}
```

**方法三：配置环境变量统一管理**

在 `~/.bashrc` 或 `~/.zshrc` 中添加（Linux/macOS）：

```bash
# 设置统一的虚拟环境目录
export UV_VENV_DIR="$HOME/.venvs"

# 创建便捷函数
venv-list() {
    find "$HOME/.venvs" -maxdepth 1 -type d | grep -v "^$HOME/.venvs$"
}

venv-activate() {
    source "$HOME/.venvs/$1/bin/activate"
}
```

### 6.4 在新项目中使用已有的虚拟环境

**⚠️ 重要提示：** 共享虚拟环境可能导致不同项目之间的依赖冲突。建议仅在以下情况使用：
- 项目依赖完全兼容
- 临时测试或开发
- 明确了解可能的风险

**方法一：通过环境变量指定虚拟环境**

```bash
# 设置环境变量指向已有的虚拟环境
export UV_PROJECT_ENVIRONMENT="/path/to/existing/venv"

# 在新项目中使用
cd new-project
uv sync  # 会在指定的虚拟环境中安装依赖
```

**方法二：在 pyproject.toml 中配置**

在 `pyproject.toml` 中添加配置：

```toml
[tool.uv]
# 指定使用已有的虚拟环境路径
environment = "/path/to/existing/venv"
```

**方法三：使用符号链接（推荐用于统一管理）**

```bash
# 1. 创建统一的虚拟环境
uv venv ~/.venvs/shared-venv --python 3.12

# 2. 在新项目中创建符号链接
cd new-project
ln -s ~/.venvs/shared-venv .venv  # Linux/macOS

# Windows 中需要使用管理员权限创建符号链接
mklink /D .venv %USERPROFILE%\.venvs\shared-venv  # Windows CMD
New-Item -ItemType SymbolicLink -Path .venv -Target ~/.venvs/shared-venv  # PowerShell
```

**方法四：复制虚拟环境（不推荐，但更安全）**

```bash
# 复制已有的虚拟环境到新项目
cp -r /path/to/existing/venv new-project/.venv  # Linux/macOS
xcopy /E /I /H existing\venv new-project\.venv  # Windows
```

**方法五：导出和导入依赖**

更安全的方式是导出依赖配置，而不是共享虚拟环境：

```bash
# 在旧项目中导出依赖
cd old-project
uv pip freeze > requirements.txt

# 在新项目中安装相同的依赖
cd new-project
uv pip install -r ../old-project/requirements.txt
```

### 6.5 创建虚拟环境

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

### 6.6 激活虚拟环境

**Linux/macOS：**
```bash
source .venv/bin/activate
```

**Windows：**
```powershell
.venv\Scripts\activate
```

### 6.7 使用虚拟环境（无需激活）

```bash
# 直接使用虚拟环境中的 Python
.venv/bin/python script.py  # Linux/macOS
.venv\Scripts\python script.py  # Windows

# 使用 uv run（自动使用项目虚拟环境）
uv run python script.py
```

### 6.8 删除虚拟环境

```bash
# 直接删除目录
rm -rf .venv  # Linux/macOS
rmdir /s .venv  # Windows
```

### 6.9 虚拟环境管理最佳实践

**1. 项目隔离原则**
- ✅ 每个项目使用独立的虚拟环境
- ✅ 虚拟环境存储在项目目录中（`.venv`）
- ❌ 避免在多个项目间共享虚拟环境

**2. 目录管理**
```bash
# 项目结构示例
my-project/
├── .venv/           # 虚拟环境（添加到 .gitignore）
├── .python-version  # Python 版本
├── pyproject.toml   # 项目配置
└── src/             # 源代码
```

**3. 清理未使用的虚拟环境**

创建清理脚本 `cleanup-venvs.sh`：

```bash
#!/bin/bash
# 清理已删除项目对应的虚拟环境

VENV_DIR="$HOME/.venvs"

if [ -d "$VENV_DIR" ]; then
    for venv in "$VENV_DIR"/*; do
        if [ -d "$venv" ]; then
            venv_name=$(basename "$venv")
            # 检查对应的项目是否还存在
            if [ ! -d "$HOME/projects/$venv_name" ]; then
                echo "删除未使用的虚拟环境: $venv_name"
                rm -rf "$venv"
            fi
        fi
    done
fi
```

**4. 虚拟环境大小管理**

```bash
# 查看虚拟环境大小
du -sh .venv  # Linux/macOS
dir .venv /s  # Windows

# 清理虚拟环境中的缓存
find .venv -type d -name "__pycache__" -exec rm -r {} +  # Linux/macOS
```

**5. 备份虚拟环境配置**

虽然不需要备份整个虚拟环境，但应该备份依赖配置：

```bash
# 导出依赖列表
uv pip freeze > requirements.txt
uv lock  # 生成锁文件

# 提交到版本控制
git add pyproject.toml uv.lock requirements.txt
```

---

## 7. 脚本管理

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

## 8. 工具管理

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

## 9. 构建与发布

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

## 10. 缓存管理

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

## 11. pip 兼容接口

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

## 12. 高级功能

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

## 13. 最佳实践

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

## 14. 故障排除

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

## 15. 命令速查表

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

## 16. 与其他工具对比

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

## 17. 总结

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
