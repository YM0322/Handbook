# Postman 使用指南

## 目录
1. [Postman 简介](#postman-简介)
2. [安装与设置](#安装与设置)
3. [界面介绍](#界面介绍)
4. [创建和发送请求](#创建和发送请求)
5. [环境变量](#环境变量)
6. [集合（Collections）](#集合collections)
7. [测试脚本](#测试脚本)
8. [Pre-request Scripts](#pre-request-scripts)
9. [Mock 服务器](#mock-服务器)
10. [文档生成](#文档生成)
11. [团队协作](#团队协作)
12. [高级功能](#高级功能)
13. [最佳实践](#最佳实践)
14. [常见问题](#常见问题)

---

## Postman 简介

Postman 是一款功能强大的 API 开发和测试工具，广泛应用于：
- API 测试
- API 开发
- API 文档生成
- 团队协作
- 自动化测试

### 主要功能
- **HTTP 请求发送**：支持 GET、POST、PUT、DELETE 等所有 HTTP 方法
- **环境变量管理**：方便切换不同环境（开发、测试、生产）
- **自动化测试**：编写测试脚本验证 API 响应
- **Mock 服务器**：模拟 API 响应，无需后端支持
- **API 文档**：自动生成和发布 API 文档
- **团队协作**：共享集合、环境变量和文档

---

## 安装与设置

### 下载安装

1. **桌面版（推荐）**
   - 访问 [Postman 官网](https://www.postman.com/downloads/)
   - 选择对应操作系统版本下载
   - 安装完成后启动应用

2. **Web 版**
   - 访问 [Postman Web](https://web.postman.co/)
   - 使用浏览器直接使用（功能略有限制）

3. **浏览器扩展（已弃用）**
   - Postman 已停止支持浏览器扩展，建议使用桌面版

### 首次设置

1. **创建账户**
   - 启动 Postman 后，选择 "Create Account" 或 "Sign In"
   - 可以使用 Google、GitHub 等账户登录
   - 免费账户即可满足大部分需求

2. **工作区设置**
   - 选择工作区类型：Personal（个人）或 Team（团队）
   - 创建或加入工作区

---

## 界面介绍

### 主界面布局

```
┌─────────────────────────────────────────────────┐
│  File  Edit  View  ...  (菜单栏)                 │
├─────────────────────────────────────────────────┤
│  [New] [Import] [Runner] [...]  (工具栏)         │
├──────────┬──────────────────────────────────────┤
│          │                                      │
│  侧边栏   │         主工作区                      │
│          │                                      │
│ Collections│    请求构建器                        │
│ Environments│   - URL 输入框                     │
│ History  │   - 请求方法选择                       │
│          │   - Headers                          │
│          │   - Body                             │
│          │   - Tests                            │
│          │                                      │
│          │   响应查看器                           │
│          │   - 状态码                            │
│          │   - 响应时间                          │
│          │   - 响应体                            │
│          │   - Headers                          │
└──────────┴──────────────────────────────────────┘
```

### 主要区域说明

1. **侧边栏（左侧）**
   - **Collections**：请求集合
   - **Environments**：环境变量
   - **History**：请求历史
   - **APIs**：API 定义

2. **主工作区（中间）**
   - **请求构建器**：构建 HTTP 请求
   - **响应查看器**：查看 API 响应

3. **右侧面板**
   - **文档**：API 文档
   - **代码生成**：生成各种语言的请求代码

---

## 创建和发送请求

### 创建新请求

1. **方法一：快速创建**
   - 点击左上角 "New" 按钮
   - 选择 "HTTP Request"
   - 输入请求名称

2. **方法二：从集合创建**
   - 右键点击集合
   - 选择 "Add Request"

### HTTP 方法

Postman 支持所有 HTTP 方法：
- **GET**：获取资源
- **POST**：创建资源
- **PUT**：更新资源（完整替换）
- **PATCH**：部分更新资源
- **DELETE**：删除资源
- **HEAD**：获取响应头
- **OPTIONS**：获取支持的 HTTP 方法

### 构建请求

#### 1. URL 设置

```
https://api.example.com/users/123
```

- 支持路径参数：`/users/:id`
- 支持查询参数：`?page=1&limit=10`
- 点击 "Params" 标签可可视化添加查询参数

#### 2. Headers 设置

点击 "Headers" 标签添加请求头：

| Key | Value |
|-----|-------|
| Content-Type | application/json |
| Authorization | Bearer your-token |
| X-API-Key | your-api-key |

**常用 Headers：**
- `Content-Type`：请求体类型
- `Authorization`：认证信息
- `Accept`：期望的响应类型
- `User-Agent`：客户端标识

#### 3. Body 设置

根据请求方法选择 Body 类型：

**a) none**
- GET、HEAD 请求通常不需要 Body

**b) form-data**
- 用于文件上传和表单数据
- 支持文本和文件字段

**c) x-www-form-urlencoded**
- 标准 HTML 表单提交格式
- 键值对形式

**d) raw**
- 原始数据
- 支持多种格式：
  - Text
  - JSON
  - XML
  - HTML
  - JavaScript

**e) binary**
- 上传二进制文件（图片、PDF 等）

**f) GraphQL**
- GraphQL 查询

**示例：JSON Body**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

### 发送请求

1. 点击右上角 "Send" 按钮
2. 或使用快捷键 `Cmd/Ctrl + Enter`

### 查看响应

响应区域显示：

1. **状态信息**
   - 状态码（200, 404, 500 等）
   - 响应时间
   - 响应大小

2. **响应体**
   - **Pretty**：格式化显示（JSON、XML 等）
   - **Raw**：原始响应
   - **Preview**：HTML 预览
   - **Visualize**：可视化显示

3. **响应头**
   - 查看服务器返回的所有响应头

4. **Cookies**
   - 查看和编辑 Cookies

---

## 环境变量

环境变量用于在不同环境间切换配置，避免硬编码。

### 创建环境

1. 点击右上角环境选择器
2. 选择 "Add" 或 "Manage Environments"
3. 点击 "Add" 创建新环境
4. 输入环境名称（如：Development、Production）

### 添加变量

在环境编辑器中添加变量：

| Variable | Initial Value | Current Value |
|----------|---------------|---------------|
| base_url | https://api-dev.example.com | https://api-dev.example.com |
| api_key | dev-key-123 | dev-key-123 |
| token | | |

**变量类型：**
- **Variable**：变量名
- **Initial Value**：初始值（保存到环境文件）
- **Current Value**：当前值（运行时使用，不保存）

### 使用变量

在请求中使用变量：

```
URL: {{base_url}}/users
Header: Authorization: Bearer {{token}}
Body: {
  "api_key": "{{api_key}}"
}
```

**语法：** `{{variable_name}}`

### 环境切换

点击右上角环境选择器，选择要使用的环境。

### 全局变量

全局变量在所有环境中都可用：
1. 点击右上角齿轮图标
2. 选择 "Manage Environments"
3. 在 "Globals" 中添加变量

---

## 集合（Collections）

集合用于组织和管理多个请求。

### 创建集合

1. 点击左侧 "New" 按钮
2. 选择 "Collection"
3. 输入集合名称和描述
4. 点击 "Create"

### 组织请求

- **文件夹**：在集合中创建文件夹分类
- **拖拽排序**：拖拽请求调整顺序
- **重命名**：右键选择 "Rename"

### 集合变量

集合级别的变量，在该集合的所有请求中可用：

1. 右键点击集合
2. 选择 "Edit"
3. 切换到 "Variables" 标签
4. 添加变量

**变量优先级：**
1. 局部变量（请求级别）
2. 数据变量（Collection Runner）
3. 环境变量
4. 集合变量
5. 全局变量

### 集合运行器（Collection Runner）

批量运行集合中的所有请求：

1. 右键点击集合
2. 选择 "Run collection"
3. 配置运行选项：
   - **Iterations**：运行次数
   - **Delay**：请求间隔（毫秒）
   - **Data**：CSV 或 JSON 数据文件
   - **Environment**：选择环境
4. 点击 "Run Collection"

### 集合导入/导出

**导出：**
1. 右键点击集合
2. 选择 "Export"
3. 选择格式（Collection v2.1 推荐）
4. 保存文件

**导入：**
1. 点击 "Import" 按钮
2. 选择文件或粘贴 JSON
3. 点击 "Import"

---

## 测试脚本

Postman 使用 JavaScript 编写测试脚本，验证 API 响应。

### 测试脚本位置

在请求的 "Tests" 标签中编写测试代码。

### 常用测试示例

#### 1. 状态码测试

```javascript
// 验证状态码为 200
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// 验证状态码在 200-299 之间
pm.test("Status code is successful", function () {
    pm.response.to.have.status(200, 299);
});
```

#### 2. 响应时间测试

```javascript
pm.test("Response time is less than 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

#### 3. 响应体测试

```javascript
// 验证响应体包含特定字段
pm.test("Response has user data", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('id');
    pm.expect(jsonData).to.have.property('name');
    pm.expect(jsonData).to.have.property('email');
});

// 验证字段值
pm.test("User ID is correct", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.id).to.eql(123);
});

// 验证字段类型
pm.test("Email is a string", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.email).to.be.a('string');
});
```

#### 4. 响应头测试

```javascript
pm.test("Content-Type is application/json", function () {
    pm.expect(pm.response.headers.get("Content-Type")).to.include("application/json");
});
```

#### 5. JSON Schema 验证

```javascript
pm.test("Response matches schema", function () {
    var schema = {
        "type": "object",
        "properties": {
            "id": {"type": "number"},
            "name": {"type": "string"},
            "email": {"type": "string"}
        },
        "required": ["id", "name", "email"]
    };
    pm.response.to.have.jsonSchema(schema);
});
```

#### 6. 数组测试

```javascript
pm.test("Response is an array", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData).to.be.an('array');
    pm.expect(jsonData.length).to.be.above(0);
});

// 验证数组元素
pm.test("All users have required fields", function () {
    var jsonData = pm.response.json();
    jsonData.forEach(function(user) {
        pm.expect(user).to.have.property('id');
        pm.expect(user).to.have.property('name');
    });
});
```

#### 7. 保存响应数据

```javascript
// 保存到环境变量
pm.test("Save user ID", function () {
    var jsonData = pm.response.json();
    pm.environment.set("user_id", jsonData.id);
    pm.environment.set("auth_token", jsonData.token);
});

// 保存到集合变量
pm.collectionVariables.set("last_user_id", jsonData.id);
```

#### 8. 条件测试

```javascript
pm.test("Conditional test", function () {
    if (pm.response.code === 200) {
        var jsonData = pm.response.json();
        pm.expect(jsonData.status).to.eql("success");
    } else {
        pm.expect(pm.response.code).to.be.oneOf([400, 401, 404]);
    }
});
```

### 测试库

Postman 使用 **Chai Assertion Library**：

- `pm.expect()`：断言
- `pm.response`：响应对象
- `pm.request`：请求对象
- `pm.environment`：环境变量
- `pm.collectionVariables`：集合变量
- `pm.globals`：全局变量

### 查看测试结果

发送请求后，在响应区域的 "Test Results" 标签查看：
- ✅ 通过的测试（绿色）
- ❌ 失败的测试（红色）
- 测试执行时间

---

## Pre-request Scripts

在发送请求前执行的脚本，用于：
- 生成动态数据
- 设置变量
- 修改请求

### 使用场景

#### 1. 生成时间戳

```javascript
// 生成当前时间戳
pm.environment.set("timestamp", Date.now());

// 生成 ISO 格式时间
pm.environment.set("current_time", new Date().toISOString());
```

#### 2. 生成随机数据

```javascript
// 随机字符串
var randomString = Math.random().toString(36).substring(7);
pm.environment.set("random_id", randomString);

// 随机数字
var randomNumber = Math.floor(Math.random() * 1000);
pm.environment.set("random_number", randomNumber);
```

#### 3. 计算签名

```javascript
// HMAC SHA256 签名示例
var crypto = require('crypto-js');
var message = pm.request.url + pm.request.body;
var secret = pm.environment.get("api_secret");
var signature = crypto.HmacSHA256(message, secret).toString();
pm.environment.set("signature", signature);
```

#### 4. 获取并设置 Token

```javascript
// 从其他请求获取 token（需要先运行该请求）
var token = pm.collectionVariables.get("auth_token");
if (token) {
    pm.request.headers.add({
        key: "Authorization",
        value: "Bearer " + token
    });
}
```

#### 5. 动态修改 URL

```javascript
// 根据环境添加路径前缀
var baseUrl = pm.environment.get("base_url");
var env = pm.environment.get("environment");
if (env === "staging") {
    pm.request.url = baseUrl + "/staging" + pm.request.url.path.join("/");
}
```

---

## Mock 服务器

Mock 服务器用于模拟 API 响应，无需真实后端。

### 创建 Mock 服务器

1. 右键点击集合
2. 选择 "Mock Collection"
3. 配置选项：
   - **Mock Server Name**：服务器名称
   - **Make mock server private**：是否私有
4. 点击 "Create Mock Server"
5. 复制 Mock URL

### 配置 Mock 响应

1. 在请求的 "Examples" 中添加示例响应
2. 点击 "Add Example"
3. 设置：
   - **Example Name**：示例名称
   - **Status Code**：状态码
   - **Response Body**：响应体
4. 保存

### 使用 Mock URL

将请求 URL 替换为 Mock URL：
```
原 URL: https://api.example.com/users
Mock URL: https://your-mock-id.mock.pstmn.io/users
```

### 动态响应

使用 `pm.variables.replaceIn()` 生成动态响应：

```javascript
// 在 Pre-request Script 中
pm.environment.set("user_id", Math.floor(Math.random() * 1000));

// 在 Mock Response Body 中
{
  "id": {{user_id}},
  "name": "Mock User",
  "created_at": "{{$timestamp}}"
}
```

---

## 文档生成

Postman 可以自动生成 API 文档。

### 生成文档

1. 右键点击集合
2. 选择 "View Documentation"
3. 文档自动生成，包含：
   - 请求描述
   - 参数说明
   - 示例请求
   - 示例响应

### 发布文档

1. 在文档页面点击 "Publish"
2. 配置发布选项
3. 获取公开链接
4. 分享给团队成员

### 添加描述

在请求中添加描述：
1. 点击请求
2. 在右侧 "Description" 区域添加 Markdown 格式描述
3. 使用代码块、表格等格式

**示例描述：**
```markdown
## 获取用户信息

根据用户 ID 获取用户详细信息。

### 参数说明
- `id` (path): 用户 ID，必填

### 响应说明
- `200`: 成功返回用户信息
- `404`: 用户不存在
```

---

## 团队协作

### 共享集合

1. 右键点击集合
2. 选择 "Share Collection"
3. 选择共享方式：
   - **Team**：共享给团队成员
   - **Public Link**：生成公开链接
   - **Workspace**：共享到工作区

### 工作区

**创建团队工作区：**
1. 点击工作区选择器
2. 选择 "Create Workspace"
3. 选择 "Team" 类型
4. 邀请团队成员

**工作区功能：**
- 共享集合
- 共享环境变量
- 协作编辑
- 版本控制

### 评论功能

在请求中添加评论：
1. 点击请求
2. 在右侧 "Comments" 区域
3. 添加评论，@ 提及团队成员

---

## 高级功能

### 1. 代码生成

Postman 可以生成各种语言的请求代码：

1. 点击请求右侧的 "Code" 按钮
2. 选择语言（cURL、JavaScript、Python、Java 等）
3. 复制代码

### 2. 拦截器（Interceptor）

捕获浏览器请求到 Postman：
1. 安装 Postman Interceptor 扩展
2. 在 Postman 中启用 Interceptor
3. 在浏览器中发送请求
4. 请求自动同步到 Postman

### 3. 代理（Proxy）

捕获 HTTP 流量：
1. 设置 → Proxy
2. 配置代理端口（默认 5555）
3. 在设备上设置代理
4. 流量自动捕获到 Postman

### 4. 监控（Monitors）

定期运行集合并监控 API 健康：

1. 点击集合
2. 选择 "Monitors"
3. 创建监控：
   - **Frequency**：运行频率
   - **Region**：运行区域
   - **Notifications**：通知设置
4. 查看监控结果和报告

### 5. 工作流（Flows）

可视化 API 工作流：
1. 点击 "New" → "Flow"
2. 拖拽组件构建工作流
3. 连接请求和逻辑
4. 运行工作流

### 6. GraphQL 支持

发送 GraphQL 查询：
1. 选择 POST 方法
2. Body 类型选择 "GraphQL"
3. 输入查询和变量

**示例：**
```graphql
query GetUser($id: ID!) {
  user(id: $id) {
    id
    name
    email
  }
}
```

**Variables：**
```json
{
  "id": "123"
}
```

### 7. WebSocket 支持

测试 WebSocket 连接：
1. 点击 "New" → "WebSocket Request"
2. 输入 WebSocket URL
3. 连接并发送消息
4. 查看实时消息

### 8. gRPC 支持

测试 gRPC 服务：
1. 点击 "New" → "gRPC Request"
2. 输入服务器地址
3. 选择服务和方法
4. 发送请求

---

## 最佳实践

### 1. 组织请求

- **使用集合和文件夹**：按功能模块组织
- **命名规范**：使用清晰的命名（如：`GET /users/:id`）
- **添加描述**：为每个请求添加说明

### 2. 使用环境变量

- **避免硬编码**：URL、密钥等使用变量
- **环境分离**：开发、测试、生产环境分开
- **敏感信息**：使用 Current Value 存储敏感数据

### 3. 编写测试

- **关键路径测试**：测试主要业务逻辑
- **边界测试**：测试边界条件
- **错误处理**：测试错误响应
- **数据验证**：验证响应数据结构

### 4. 使用 Pre-request Scripts

- **动态数据**：生成时间戳、随机数等
- **认证**：自动设置认证信息
- **签名计算**：自动计算 API 签名

### 5. 文档维护

- **及时更新**：API 变更时更新文档
- **详细描述**：包含参数说明、示例
- **版本控制**：使用版本号管理 API

### 6. 团队协作

- **共享集合**：团队共享 API 集合
- **统一环境**：使用统一的环境变量
- **代码审查**：审查测试脚本和文档

### 7. 性能测试

- **响应时间**：设置响应时间阈值
- **批量测试**：使用 Collection Runner
- **监控**：使用 Monitors 持续监控

### 8. 安全实践

- **敏感信息**：不在代码中硬编码密钥
- **使用变量**：敏感数据存储在变量中
- **定期更新**：定期更新 API 密钥

---

## 常见问题

### Q1: 如何保存请求历史？

A: Postman 自动保存请求历史，在左侧 "History" 中查看。可以：
- 右键历史记录保存到集合
- 设置中配置历史记录保留时间

### Q2: 如何导入 cURL 命令？

A: 
1. 点击 "Import"
2. 选择 "Raw text"
3. 粘贴 cURL 命令
4. 点击 "Import"

### Q3: 变量不生效怎么办？

A: 检查：
1. 变量名拼写是否正确（区分大小写）
2. 是否选择了正确的环境
3. 变量作用域是否正确
4. 语法是否正确：`{{variable_name}}`

### Q4: 如何测试需要认证的 API？

A: 
1. 在 Headers 中添加 `Authorization` 头
2. 使用环境变量存储 token
3. 在 Pre-request Script 中自动设置 token
4. 使用 Postman 的认证类型（OAuth、Basic Auth 等）

### Q5: 如何批量运行测试？

A: 
1. 使用 Collection Runner
2. 配置迭代次数和数据文件
3. 查看测试报告

### Q6: 测试脚本中可以使用哪些库？

A: Postman 内置支持：
- **Chai**：断言库
- **Lodash**：工具库
- **crypto-js**：加密库
- **moment**：时间处理库（需手动引入）

### Q7: 如何共享环境变量？

A: 
1. 导出环境文件
2. 团队成员导入
3. 或使用团队工作区共享

### Q8: Mock 服务器响应如何动态化？

A: 
1. 使用 `{{$variable}}` 语法
2. 在 Pre-request Script 中设置变量
3. 使用内置变量如 `{{$timestamp}}`、`{{$randomInt}}`

### Q9: 如何调试测试脚本？

A: 
1. 使用 `console.log()` 输出调试信息
2. 在 Postman Console 查看（View → Show Postman Console）
3. 检查测试结果中的错误信息

### Q10: 免费版和付费版有什么区别？

A: 
- **免费版**：基本功能完整，适合个人使用
- **付费版**：更多协作功能、高级监控、优先支持

---

## 快捷键

### Mac
- `Cmd + Enter`：发送请求
- `Cmd + /`：切换注释
- `Cmd + K`：快速搜索
- `Cmd + ,`：打开设置

### Windows/Linux
- `Ctrl + Enter`：发送请求
- `Ctrl + /`：切换注释
- `Ctrl + K`：快速搜索
- `Ctrl + ,`：打开设置

---

## 总结

Postman 是一款功能强大的 API 测试工具，掌握以下核心功能：

1. ✅ **创建和发送请求**：支持所有 HTTP 方法
2. ✅ **环境变量**：管理不同环境配置
3. ✅ **集合管理**：组织和管理 API 请求
4. ✅ **测试脚本**：自动化 API 测试
5. ✅ **Mock 服务器**：模拟 API 响应
6. ✅ **文档生成**：自动生成 API 文档
7. ✅ **团队协作**：共享和协作开发

通过本指南，您应该能够熟练使用 Postman 进行 API 开发和测试。建议从基础功能开始，逐步掌握高级特性。

---

## 参考资源

- [Postman 官方文档](https://learning.postman.com/)
- [Postman 社区](https://community.postman.com/)
- [Postman 博客](https://blog.postman.com/)
- [Postman YouTube 频道](https://www.youtube.com/c/Postman)

---

**最后更新：** 2024年

**版本：** 1.0

