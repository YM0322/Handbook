## Shell 脚本编写与使用说明书

> 适用对象：使用 macOS / Linux / WSL 的初学者，目标是**从零掌握 Shell 脚本的编写与使用**。

---

## 1. 基础概念与环境准备

- **什么是 Shell？**  
  - **Shell**：用户与操作系统之间的“壳”程序，用来解释你输入的命令。  
  - 常见类型：`sh`、`bash`、`zsh`、`fish` 等。  
  - 本说明主要以 **bash/zsh 语法** 为主（在 macOS 上默认就是这些）。

- **什么是 Shell 脚本？**  
  - 把一系列 shell 命令写进一个文本文件中，交给 shell 一次性执行。  
  - 优点：**自动化、可重复、可分享、可维护**。

- **如何确认当前使用的 Shell？**

```bash
echo $SHELL
```

- **建议的编辑器**  
  - 命令行：`nano`、`vim`、`nvim`。  
  - 图形界面：VS Code、Cursor 等（推荐）。

---

## 2. 编写你的第一个 Shell 脚本

### 2.1 创建脚本文件

在终端中进入我的工作目录，例如：

```bash
cd /Users/ming.yang/Desktop/ideal
```

创建一个脚本文件：

```bash
touch hello.sh
```

用编辑器打开（任选其一）：

```bash
code hello.sh        # VS Code / Cursor
# 或
nano hello.sh        # 终端编辑
```

### 2.2 Shebang 与最简单示例

在 `hello.sh` 中写入：

```bash
#!/usr/bin/env bash

echo "Hello, Shell!"
```

- **第一行：Shebang**
  - `#!/usr/env bash` 表示这个脚本由 `bash` 来解释执行。  
  - 常见替代：`#!/bin/bash`、`#!/bin/zsh` 等。

- **第二行：`echo`**
  - 在终端中输出一行文本。

### 2.3 赋予可执行权限并运行

给脚本加执行权限：

```bash
chmod +x hello.sh
```

执行脚本：

```bash
./hello.sh
```

如果当前目录不在 `PATH` 中，就必须写 `./`。

也可以不加执行权限，用解释器显式调用：

```bash
bash hello.sh
```

---

## 3. 变量与基本语法

### 3.1 变量定义与使用

- **赋值时：不能有空格**

```bash
name="Ming"
age=18
```

- **取值时：必须加 `$`**

```bash
echo "Name is $name, age is $age"
```

- **推荐：用花括号防止歧义**

```bash
echo "Hello, ${name}!"
```

### 3.2 字符串与引号

- **双引号 `"`**：会解析变量与转义符。
- **单引号 `'`**：原样输出，不解析变量。

```bash
echo "My name is $name"   # 输出变量值
echo 'My name is $name'   # 输出字面：$name
```

### 3.3 命令替换

把一个命令的输出赋值给变量：

```bash
today=$(date +"%Y-%m-%d")
echo "Today is $today"
```

---

## 4. 条件判断与分支

### 4.1 `if` 语法结构

```bash
if 条件; then
  # 条件为真时执行
elif 其它条件; then
  # 另一个条件为真时执行
else
  # 都不满足时执行
fi
```

注意：`then` 可以放在同一行，要用 `;` 隔开。

### 4.2 常用条件测试

使用 `[` 或 `test` 命令（本质一样）：

- **字符串比较**

```bash
str="abc"
if [ "$str" = "abc" ]; then
  echo "equal"
fi
```

- **整数比较**

```bash
x=5
if [ "$x" -gt 3 ]; then
  echo "x > 3"
fi
```

常用运算符：
- **`-eq`**：等于
- **`-ne`**：不等于
- **`-gt`**：大于
- **`-ge`**：大于等于
- **`-lt`**：小于
- **`-le`**：小于等于

- **文件判断**

```bash
file="hello.sh"

if [ -f "$file" ]; then
  echo "普通文件存在"
fi

if [ -d "$file" ]; then
  echo "这是一个目录"
fi
```

常用文件判断：
- **`-e`**：存在（文件或目录）
- **`-f`**：是普通文件
- **`-d`**：是目录
- **`-r`**：可读
- **`-w`**：可写
- **`-x`**：可执行

### 4.3 复合条件

```bash
if [ "$x" -gt 3 ] && [ "$x" -lt 10 ]; then
  echo "3 < x < 10"
fi
```

也可以写成：

```bash
if [ "$x" -gt 3 -a "$x" -lt 10 ]; then
  echo "3 < x < 10"
fi
```

---

## 5. 循环语句

### 5.1 `for` 循环

- **遍历列表**

```bash
for name in Alice Bob Charlie; do
  echo "Hello, $name"
done
```

- **遍历数字序列**

```bash
for i in {1..5}; do
  echo "i = $i"
done
```

在某些旧版本 `sh` 中可能不支持 `{1..5}`，可以用 `seq`：

```bash
for i in $(seq 1 5); do
  echo "i = $i"
done
```

### 5.2 `while` 循环

```bash
count=1
while [ "$count" -le 5 ]; then
  echo "count = $count"
  count=$((count + 1))
done
```

### 5.3 `until` 循环

`until` 与 `while` 相反：**直到条件为真才停止**。

```bash
count=1
until [ "$count" -gt 5 ]; then
  echo "count = $count"
  count=$((count + 1))
done
```

---

## 6. 函数与脚本结构化

### 6.1 定义与调用函数

```bash
#!/usr/bin/env bash

say_hello() {
  echo "Hello, $1"
}

say_hello "Ming"
say_hello "World"
```

- 函数名后面不加括号也可以：`say_hello { ... }`，但加上 `()` 更清晰。
- 函数参数用 `$1`、`$2`、`$3`……访问。

### 6.2 返回值与退出状态

- 每个命令执行后，都有一个 **退出状态码** `\$?`：
  - `0` 表示成功；
  - 非 `0` 表示错误。

```bash
ls /tmp
echo "上一个命令的退出状态是：$?"
```

- 在函数或脚本中使用 `return` / `exit` 设置状态码：

```bash
my_func() {
  if [ -f "$1" ]; then
    return 0
  else
    return 1
  fi
}

my_func "hello.sh"
echo "函数返回值：$?"
```

---

## 7. 脚本参数处理

### 7.1 位置参数

执行脚本时：

```bash
./myscript.sh arg1 arg2 arg3
```

在脚本内部：

- **`$0`**：脚本自身的文件名；
- **`$1`、`$2`、...**：第 1、2、... 个参数；
- **`$#`**：参数个数；
- **`$@`**：所有参数（保持分隔）；
- **`$*`**：所有参数（作为一个整体字符串）。

示例：

```bash
#!/usr/bin/env bash

echo "脚本名：$0"
echo "参数个数：$#"
echo "第一个参数：$1"
echo "全部参数：$@"
```

### 7.2 简单参数校验

```bash
#!/usr/bin/env bash

if [ "$#" -lt 1 ]; then
  echo "用法：$0 <name>"
  exit 1
fi

name="$1"
echo "Hello, $name"
```

---

## 8. 输入输出与管道

### 8.1 标准输入 / 输出 / 错误

- **标准输入（stdin）**：文件描述符 `0`，通常是键盘；
- **标准输出（stdout）**：文件描述符 `1`，正常输出；
- **标准错误（stderr）**：文件描述符 `2`，错误信息。

### 8.2 重定向

- **把输出写入文件（覆盖）**

```bash
echo "Hello" > output.txt
```

- **把输出追加到文件**

```bash
echo "World" >> output.txt
```

- **只重定向错误输出**

```bash
command 2> error.log
```

- **同时重定向标准输出和错误输出**

```bash
command > all.log 2>&1
```

### 8.3 管道

**管道 `|`：把前一个命令的输出当成后一个命令的输入。**

```bash
ps aux | grep "bash"
ls -l | wc -l
```

---

## 9. 实用示例

### 9.1 批量重命名文件

需求：把当前目录下所有 `.txt` 文件重命名为 `.bak` 后缀。

```bash
#!/usr/bin/env bash

for file in *.txt; do
  [ -e "$file" ] || continue   # 避免没有匹配时的错误

  new_name="${file%.txt}.bak"
  echo "Rename: $file -> $new_name"
  mv "$file" "$new_name"
done
```

### 9.2 简单备份脚本

```bash
#!/usr/bin/env bash

set -e  # 遇到错误立即退出

SRC_DIR="$1"
DEST_DIR="$2"

if [ "$#" -ne 2 ]; then
  echo "用法：$0 <源目录> <目标目录>"
  exit 1
fi

if [ ! -d "$SRC_DIR" ]; then
  echo "源目录不存在：$SRC_DIR"
  exit 1
fi

mkdir -p "$DEST_DIR"

today=$(date +"%Y%m%d_%H%M%S")
backup_name="backup_${today}.tar.gz"

tar -czf "${DEST_DIR}/${backup_name}" -C "$SRC_DIR" .

echo "备份完成：${DEST_DIR}/${backup_name}"
```

使用：

```bash
chmod +x backup.sh
./backup.sh /path/to/src /path/to/backup_dir
```

---

## 10. 常见技巧与最佳实践

- **1）开头加安全选项**

```bash
#!/usr/bin/env bash
set -euo pipefail
```

- **`-e`**：一旦有命令出错（非 0），脚本立即退出；  
- **`-u`**：使用未定义变量时报错；  
- **`-o pipefail`**：管道中任何一段失败都视为失败。

- **2）给脚本加使用说明（帮助信息）**

```bash
usage() {
  cat <<EOF
用法：$0 <参数1> <参数2>

示例：
  $0 foo bar
EOF
}

if [ "$#" -lt 2 ]; then
  usage
  exit 1
fi
```

- **3）始终给变量加双引号**

```bash
rm -rf "$dir"      # 而不是 rm -rf $dir
```

避免变量中有空格或特殊字符时出问题。

- **4）调试脚本**

```bash
bash -x your_script.sh
```

或在脚本中临时加：

```bash
set -x   # 开启调试输出
# ... 这里的命令会打印出执行细节 ...
set +x   # 关闭调试输出
```

- **5）统一风格**
  - 缩进统一（2 或 4 空格）；  
  - 函数、变量命名有意义；  
  - 常量用大写，如 `LOG_DIR`, `BACKUP_DIR`。

---

## 11. 在系统中“安装”你的脚本

当写的脚本越来越多，可以把它们放到一个目录，并加入 `PATH`，在任何地方都能直接运行。

### 11.1 建议目录

例如：

```bash
mkdir -p "$HOME/bin"
```

把脚本移动或复制到 `~/bin`：

```bash
cp hello.sh "$HOME/bin/"
chmod +x "$HOME/bin/hello.sh"
```

### 11.2 修改 `PATH`

在 `~/.zshrc` 或 `~/.bashrc` 中加入：

```bash
export PATH="$HOME/bin:$PATH"
```

保存后执行：

```bash
source ~/.zshrc   # 或 ~/.bashrc
```

之后，你就可以在任何目录直接执行：

```bash
hello.sh
```

---

## 12. 推荐的学习与实践路径

- **第 1 阶段：熟悉命令行**
  - 多用 `ls`、`cd`、`cp`、`mv`、`rm`、`cat`、`grep`、`find` 等基础命令；
  - 学习用 `|`（管道）和 `>`/`>>`（重定向）组合命令。

- **第 2 阶段：小脚本自动化**
  - 写脚本批量重命名、打包、清理日志、启动服务等；
  - 每次重复三次以上的手动操作，都尝试写脚本。

- **第 3 阶段：结构化与健壮性**
  - 使用函数拆分逻辑；  
  - 加入参数校验、错误处理、日志输出；  
  - 使用 `set -euo pipefail` 提高健壮性。

- **第 4 阶段：阅读优秀脚本**
  - 阅读系统自带的脚本（如 `/usr/local/bin`、`/usr/bin` 下的脚本）；  
  - 从开源项目中学习脚本的结构和风格。

---

## 13. 下一步建议

- **动手练习**：  
  - 在当前项目目录（`/Users/ming.yang/Desktop/ideal`）下，按照本说明书创建 `hello.sh`、`backup.sh` 等脚本并运行；  
  - 逐步把日常经常做的重复操作脚本化。

- **如需进阶内容**（例如：`getopts` 参数解析、数组、关联数组、更复杂的错误处理与日志系统等），可以在本仓库中新建一个 `shell_advanced.md`，继续系统学习与记录。 


