## Shell 脚本进阶说明书

> 建议先熟悉 `shell_script_tutorial.md` 中的基础内容，再阅读本进阶部分。  
> 主要面向经常写脚本、希望提高**可维护性、健壮性和可读性**的使用者。

---

## 1. 通用脚本骨架与错误处理

### 1.1 推荐的脚本模板

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

SCRIPT_NAME=$(basename "$0")

log_info()  { echo "[INFO]  [$SCRIPT_NAME] $*" >&1; }
log_warn()  { echo "[WARN]  [$SCRIPT_NAME] $*" >&2; }
log_error() { echo "[ERROR] [$SCRIPT_NAME] $*" >&2; }

cleanup() {
  # 在这里做收尾工作，比如删除临时文件
  :
}

on_error() {
  local exit_code=$?
  local line_no=${1:-?}
  log_error "在行 $line_no 发生错误，退出码：$exit_code"
  cleanup
  exit "$exit_code"
}

trap 'on_error $LINENO' ERR
trap 'cleanup' EXIT

main() {
  log_info "脚本开始执行"
  # 你的主逻辑
  log_info "脚本执行结束"
}

main "$@"
```

**说明要点：**
- `set -euo pipefail`：默认让脚本“宁可早死，避免带病运行”；  
- 自定义 `log_*` 函数，统一输出格式；  
- `trap` 结合 `ERR` / `EXIT`：自动捕获错误和退出，做清理工作；  
- 使用 `main` 函数，让脚本结构更清晰，参数通过 `"$@"` 传入。

---

## 2. 高级参数解析：`getopts`

### 2.1 基本模式

`getopts` 用来解析短选项，例如：

```bash
#!/usr/bin/env bash
set -euo pipefail

show_help=false
verbose=false
output=""

usage() {
  cat <<EOF
用法：$0 [-h] [-v] [-o 输出文件] [参数...]

选项：
  -h          显示帮助
  -v          输出详细信息
  -o <file>   指定输出文件
EOF
}

while getopts ":hvo:" opt; do
  case "$opt" in
    h)
      show_help=true
      ;;
    v)
      verbose=true
      ;;
    o)
      output="$OPTARG"
      ;;
    \?)
      echo "未知选项：-$OPTARG" >&2
      usage
      exit 1
      ;;
    :)
      echo "选项 -$OPTARG 需要参数" >&2
      usage
      exit 1
      ;;
  esac
done

shift $((OPTIND - 1))

if [ "$show_help" = true ]; then
  usage
  exit 0
fi

if [ -n "$output" ]; then
  echo "输出文件：$output"
fi

if [ "$verbose" = true ]; then
  echo "开启详细模式"
fi

echo "剩余位置参数：$*"
```

**关键点：**
- `:hvo:` 中，`:` 表示该选项后必须带参数；  
- `\?)` 分支处理未知选项；  
- `:)` 分支处理缺失参数；  
- `shift $((OPTIND - 1))` 把已经被解析的选项从参数列表中移除。

### 2.2 支持长选项的常见做法

`getopts` 本身不支持长选项（如 `--help`），可以结合简单的预处理：

```bash
TEMP_ARGS=()
for arg in "$@"; do
  case "$arg" in
    --help)    TEMP_ARGS+=("-h") ;;
    --verbose) TEMP_ARGS+=("-v") ;;
    --output=*)
      TEMP_ARGS+=("-o")
      TEMP_ARGS+=("${arg#*=}")
      ;;
    *)
      TEMP_ARGS+=("$arg")
      ;;
  esac
done

set -- "${TEMP_ARGS[@]}"
```

然后再照常用 `getopts` 解析，即可同时支持短选项和常见的长选项形式。

---

## 3. 数组与关联数组

> 注意：数组与关联数组为 **bash 特性**，在纯 `sh` 中不可用，因此脚本 shebang 最好写 `#!/usr/bin/env bash`。

### 3.1 普通数组

```bash
fruits=("apple" "banana" "cherry")

echo "第一个元素：${fruits[0]}"
echo "全部元素：${fruits[@]}"
echo "元素个数：${#fruits[@]}"

for fruit in "${fruits[@]}"; do
  echo "fruit = $fruit"
done
```

追加元素：

```bash
fruits+=("orange")
```

### 3.2 关联数组

```bash
declare -A user_ages
user_ages["Alice"]=20
user_ages["Bob"]=25

echo "Alice 的年龄：${user_ages["Alice"]}"

for name in "${!user_ages[@]}"; do
  echo "$name: ${user_ages[$name]}"
done
```

关联数组适合用来存配置、计数器、键值映射等。

---

## 4. 字符串与参数扩展技巧

Bash 提供了非常强大的“参数扩展”语法，可以减少对外部命令的依赖。

### 4.1 默认值与必填校验

```bash
name="${1:-default_name}"      # 若 $1 为空，则使用 default_name
name="${1:?必须提供名称参数}"  # 若 $1 为空，则报错并退出
```

### 4.2 前缀 / 后缀删除

```bash
path="/usr/local/bin/bash"

echo "${path#*/}"      # 删除最短前缀  -> "usr/local/bin/bash"
echo "${path##*/}"     # 删除最长前缀  -> "bash"

file="archive.tar.gz"
echo "${file%.gz}"     # 删除最短后缀 .gz      -> "archive.tar"
echo "${file%%.*}"     # 删除最长后缀 .tar.gz -> "archive"
```

### 4.3 子串替换

```bash
str="hello world world"

echo "${str/world/you}"     # 替换第一个 world -> "hello you world"
echo "${str//world/you}"    # 替换所有 world   -> "hello you you"
```

---

## 5. 正则工具与文本处理：`grep` / `sed` / `awk`

> Shell 脚本中 80% 的高级用法都与“文本处理”有关。

### 5.1 `grep`：过滤

```bash
grep "ERROR" app.log             # 包含 ERROR 的行
grep -i "error" app.log          # 忽略大小写
grep -n "ERROR" app.log          # 显示行号
grep -v "DEBUG" app.log          # 过滤掉包含 DEBUG 的行
```

常见用法：配合管道过滤：

```bash
ps aux | grep "nginx" | grep -v "grep"
```

### 5.2 `sed`：简单替换与编辑

- **行内替换**

```bash
sed 's/foo/bar/' file.txt         # 每行只替换第一个 foo
sed 's/foo/bar/g' file.txt        # 每行替换所有 foo
```

- **直接改写文件（注意备份）**

```bash
sed -i.bak 's/foo/bar/g' file.txt
```

### 5.3 `awk`：按列处理

```bash
awk '{print $1, $3}' file.txt

# 只打印第 2 列等于 "ERROR" 的行的第 1 列和第 4 列
awk '$2 == "ERROR" {print $1, $4}' app.log

# 统计第 1 列出现的次数
awk '{count[$1]++} END {for (k in count) print k, count[k]}' file.txt
```

---

## 6. 日志与调试策略

### 6.1 分级日志与输出到文件

```bash
LOG_FILE="/tmp/my_script.log"

log() {
  local level="$1"; shift
  local ts
  ts=$(date +"%Y-%m-%d %H:%M:%S")
  echo "[$ts] [$level] $*" | tee -a "$LOG_FILE" >&2
}

log "INFO" "脚本启动"
log "WARN" "磁盘空间较低"
log "ERROR" "操作失败，退出"
```

**技巧：**
- 用 `tee -a` 一边输出到终端，一边追加到日志文件；  
- 严肃脚本中可以把普通信息输出到 `stdout`，错误信息输出到 `stderr`。

### 6.2 条件调试开关

```bash
DEBUG="${DEBUG:-0}"

debug() {
  if [ "$DEBUG" -eq 1 ]; then
    echo "[DEBUG] $*" >&2
  fi
}

debug "这条信息只在 DEBUG=1 时显示"
```

运行时可以：

```bash
DEBUG=1 ./your_script.sh
```

---

## 7. 并行与任务控制

### 7.1 简单后台任务

```bash
long_task() {
  echo "开始长任务..."
  sleep 5
  echo "长任务结束"
}

long_task &
pid=$!

echo "后台任务 PID: $pid"

wait "$pid"
echo "后台任务已完成"
```

### 7.2 简单并行处理示例

```bash
urls=("https://example.com/a" "https://example.com/b" "https://example.com/c")

for url in "${urls[@]}"; do
  {
    echo "开始下载：$url"
    curl -s "$url" -o "/tmp/$(basename "$url")"
    echo "完成下载：$url"
  } &
done

wait
echo "全部下载任务完成"
```

> 更高级的并行控制可以考虑使用 `xargs -P` 或 `GNU parallel` 等工具。

---

## 8. 配置文件与环境管理

### 8.1 `.env` / 配置文件加载

```bash
CONFIG_FILE=".env"

if [ -f "$CONFIG_FILE" ]; then
  # shellcheck disable=SC1090
  source "$CONFIG_FILE"
else
  echo "配置文件不存在：$CONFIG_FILE" >&2
fi

echo "DB_HOST=${DB_HOST:-localhost}"
echo "DB_PORT=${DB_PORT:-3306}"
```

`.env` 示例：

```bash
DB_HOST=127.0.0.1
DB_PORT=5432
```

### 8.2 根据环境切换行为

```bash
ENV="${ENV:-dev}"  # dev / test / prod

case "$ENV" in
  dev)
    API_URL="https://dev-api.example.com"
    ;;
  test)
    API_URL="https://test-api.example.com"
    ;;
  prod)
    API_URL="https://api.example.com"
    ;;
  *)
    echo "未知环境：$ENV" >&2
    exit 1
    ;;
esac

echo "当前环境：$ENV"
echo "使用 API 地址：$API_URL"
```

---

## 9. 小型脚本项目的组织

当脚本越来越多，建议像“小项目”一样组织：

```text
my-scripts/
  bin/
    backup.sh
    deploy.sh
  lib/
    common.sh
    logging.sh
  config/
    dev.env
    prod.env
  README.md
```

### 9.1 公共函数库

`lib/common.sh`：

```bash
#!/usr/bin/env bash

set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

log_info()  { echo "[INFO]  $*" >&2; }
log_error() { echo "[ERROR] $*" >&2; }
```

`bin/backup.sh`：

```bash
#!/usr/bin/env bash
set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
# 载入公共函数
# shellcheck disable=SC1090
source "$SCRIPT_DIR/../lib/common.sh"

log_info "执行备份逻辑..."
```

通过这种方式可以复用日志、错误处理、参数解析等通用能力。

---

## 10. 静态检查与测试（ShellCheck / bats）

### 10.1 ShellCheck：发现常见错误

安装 `shellcheck` 后：

```bash
shellcheck your_script.sh
```

它能发现：
- 未加引号的变量；  
- 使用未定义变量；  
- 不兼容的用法等。

### 10.2 简单测试思路

即使不用专门测试框架，也可以写非常简单的测试脚本：

```bash
#!/usr/bin/env bash
set -euo pipefail

output=$(./your_script.sh foo bar)

if [[ "$output" == *"期望的字符串"* ]]; then
  echo "测试通过"
else
  echo "测试失败：输出 = $output"
  exit 1
fi
```

> 可以进一步了解 `bats` 这样的 Shell 测试框架。

---

## 11. 建议的进阶实践路径

- **1）把已有脚本改造成“标准骨架”**  
  - 加上 `set -euo pipefail`、`trap`、`main` 函数；  
  - 把重复代码抽到公共的 `common.sh` / `logging.sh` 中。

- **2）系统使用 `getopts`**  
  - 为常用脚本加上 `-h/-v/-f/-o` 等选项；  
  - 练习支持简单长选项（`--help`、`--output=` 等）。

- **3）以“文本处理”为中心刷题**  
  - 多用 `grep/sed/awk` 去做日志分析、小数据处理；  
  - 把一连串的命令封装成脚本，并加上参数和日志。

- **4）引入质量保障**  
  - 所有脚本通过 `shellcheck`；  
  - 关键脚本有最基本的“回归测试”脚本。




