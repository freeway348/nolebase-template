# 📋 智能表单映射填充系统 - 毕业设计说明文档

## 一、项目概述

### 1.1 项目背景与意义

本系统是一个**基于大语言模型的智能表单映射填充系统**，旨在解决以下痛点：

| 问题场景 | 传统方式 | 本系统方案 |
|---------|---------|-----------|
| 数据录入效率低 | 人工逐条填写表单 | 自动批量填充 |
| 字段匹配困难 | 需要人工理解字段含义 | 大模型智能匹配 |
| 多源数据接入复杂 | 不同格式需要不同处理 | 统一接口接入 Excel/SQL/网页 |
| 重复劳动 | 相同数据多次录入 | 一次映射，重复使用 |

### 1.2 核心功能

1. **多源数据接入**：支持 Excel、SQL 文件、网页表格三种数据源
2. **智能字段映射**：基于大语言模型自动推荐字段对应关系
3. **自动表单填充**：使用浏览器自动化技术填写目标表单
4. **结果导出**：支持 Excel/CSV 格式导出

---

## 二、技术选型（毕业答辩重点）

### 2.1 技术栈总览

| 分类 | 技术 | 版本 | 选择理由 |
|------|------|------|----------|
| 后端框架 | FastAPI | 0.100+ | 高性能、自动文档、类型安全 |
| 前端框架 | Vue 3 | 3.3+ | 简单易用、响应式、CDN 部署 |
| 大模型 | 阿里云百炼 (Qwen) | - | 中文理解优秀、国内稳定 |
| AI 框架 | LangChain | 0.1+ | Agent 架构、工具调用 |
| 浏览器自动化 | Selenium | 4.10+ | 真实浏览器、支持动态页面 |
| 数据处理 | pandas | 2.0+ | 强大的数据处理能力 |
| 数据库 | SQLite (可选) | - | 轻量级、无需额外部署 |

### 2.2 关键技术选型详解

#### ✅ 为什么选择 FastAPI？

**答辩要点**：
1. **高性能**：基于 Starlette，性能接近 Node.js 和 Go
2. **自动文档**：访问 `/docs` 即可查看交互式 API 文档，便于测试和演示
3. **类型安全**：基于 Python 类型提示，自动参数校验，减少错误
4. **异步支持**：原生支持异步操作，适合 IO 密集型任务（网络请求、文件读写）
5. **生态完善**：丰富的中间件和第三方库支持

**代码位置**：[backend/main.py](file:///d:/Essay/WebFill/backend/main.py)

#### ✅ 为什么选择 Vue 3？

**答辩要点**：
1. **简单易用**：CDN 版本无需 npm 安装和构建工具，开箱即用
2. **响应式系统**：数据驱动视图，无需手动操作 DOM
3. **Composition API**：代码组织更清晰，逻辑复用性强
4. **轻量级**：适合小项目和演示系统

**代码位置**：[fronted/main.js](file:///d:/Essay/WebFill/fronted/main.js)

#### ✅ 为什么选择阿里云百炼 (DashScope)？

**答辩要点**：
1. **中文理解优秀**：Qwen 模型在中文语义理解上表现出色
2. **本地化服务**：国内访问速度快，稳定性高
3. **价格合理**：相比国外服务（OpenAI）更具性价比
4. **企业级支持**：阿里云提供稳定的 API 服务

**代码位置**：[backend/agents/matching_agent.py](file:///d:/Essay/WebFill/backend/agents/matching_agent.py)

#### ✅ 为什么选择 LangChain？

**答辩要点**：
1. **Agent 架构**：支持智能体自主决策，根据需求调用工具
2. **工具集成**：可以方便地添加自定义工具供大模型使用
3. **标准化接口**：提供统一的方式与不同大模型交互
4. **提示词管理**：便于管理和优化大模型提示词

**代码位置**：[backend/agents/matching_agent.py](file:///d:/Essay/WebFill/backend/agents/matching_agent.py)

#### ✅ 为什么选择 Selenium？

**答辩要点**：
1. **真实浏览器**：可以处理 JavaScript 渲染的动态页面
2. **模拟人类操作**：支持点击、输入、选择等真实交互
3. **跨浏览器支持**：可以使用 Chrome、Firefox 等多种浏览器
4. **成熟稳定**：自动化测试领域的标准工具

**代码位置**：[backend/tools/browser_filler_tool.py](file:///d:/Essay/WebFill/backend/tools/browser_filler_tool.py)

---

## 三、系统架构分层

### 3.1 整体架构图

```
┌─────────────────────────────────────────────────────────────────────┐
│                             前端层 (fronted)                         │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────┐  │
│  │  [Vue 3] 用户界面                  │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │
│  │  │ 上传源数据  │  │ 解析目标表单 │  │ 字段映射    │  │ 填充与导出  │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘  │
│  └─────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              │ HTTP
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                             后端层 (backend)                         │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────┐  │
│  │                           FastAPI                                 │  │
│  │  ┌───────────────────────────────────────────────────────────┐  │  │
│  │  │  API 层 (API Layer)                                        │  │  │
│  │  │  /api/source/preview  /api/target/preview  /api/mapping/recommend  │  │  │
│  │  │  /api/fill/execute  /api/export                            │  │  │
│  │  └───────────────────────────────────────────────────────────┘  │  │
│  │  ┌───────────────────────────────────────────────────────────┐  │  │
│  │  │  services 层 (业务服务层)                                   │  │  │
│  │  │  DataIngestionService  FieldMatchingService  DataFillingService  │  │  │
│  │  └───────────────────────────────────────────────────────────┘  │  │
│  │  ┌───────────────────────────────────────────────────────────┐  │  │
│  │  │  agents 层 (智能体层)      tools 层 (工具层)          │  │  │
│  │  │  MatchingAgent  FillAgent  ExcelTool  SQLFileTool    │  │  │
│  │  │  WebScraperTool  DataCleanerTool  BrowserFillerTool  OptionMatchingTool  │  │  │
│  │  └───────────────────────────────────────────────────────────┘  │  │
│  │  ┌───────────────────────────────────────────────────────────┐  │  │
│  │  │  models 层 (数据模型层)          utils 层 (工具函数层)       │  │  │
│  │  │  Pydantic 模型定义              通用工具函数              │  │  │
│  │  └───────────────────────────────────────────────────────────┘  │  │
│  └─────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              │ API
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                          外部服务层                                   │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────┐  │
│  │  阿里云百炼 API (Qwen Turbo)                                      │  │
│  │  大语言模型服务，提供语义理解能力                                  │  │
│  └─────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 前端层 (fronted)

**设计内容**：基于 Vue 3 的单页面应用，采用 CDN 方式引入，无需构建工具

**职责**：
- 提供用户交互界面，包含 4 个主要功能模块：上传源数据、解析目标表单、字段映射、填充导出
- 展示数据预览和映射结果
- 发送 API 请求到后端服务
- 处理用户输入和操作反馈

**实现功能**：
- 数据源选择（Excel/SQL/URL）
- 文件上传和 URL 输入
- 数据预览表格展示
- 字段映射可视化编辑
- 表单填充结果展示
- 导出格式选择

**关键文件**：[fronted/index.html](file:///d:/Essay/WebFill/fronted/index.html)、[fronted/main.js](file:///d:/Essay/WebFill/fronted/main.js)

### 3.3 后端层 (backend)

后端采用模块化分层架构，各层职责清晰，解耦性强。

#### 3.3.1 api 层

**设计内容**：基于 FastAPI 的 RESTful API 路由定义

**职责**：
- 接收前端 HTTP 请求
- 参数校验和类型转换
- 路由分发到对应的业务服务
- 返回标准化的响应数据

**实现功能**：
- `POST /api/source/preview` - 预览源数据
- `GET /api/target/preview` - 预览目标表单结构
- `POST /api/mapping/recommend` - 获取智能映射推荐
- `POST /api/fill/execute` - 执行表单填充
- `GET /api/export` - 导出结果数据

**关键文件**：[backend/api/endpoints.py](file:///d:/Essay/WebFill/backend/api/endpoints.py)

#### 3.3.2 services 层（业务服务层）

**设计内容**：核心业务逻辑层，作为 API 层和底层工具的中间协调者

**职责**：
- 封装业务流程，编排智能体和工具的调用
- 数据流转和格式转换
- 业务规则的实现和管理

**实现功能**：
- **DataIngestionService**：根据数据源类型调度对应工具读取数据，调用 DataCleanerTool 清洗，返回统一 DataFrame
- **FieldMatchingService**：接收源列和目标字段特征，构造提示词后调用 MatchingAgent
- **DataFillingService**：根据用户确认的映射规则，调用 FillAgent 驱动浏览器自动化填充

**关键文件**：[backend/services/data_ingestion.py](file:///d:/Essay/WebFill/backend/services/data_ingestion.py)、[backend/services/field_matching.py](file:///d:/Essay/WebFill/backend/services/field_matching.py)、[backend/services/data_filling.py](file:///d:/Essay/WebFill/backend/services/data_filling.py)

#### 3.3.3 agents 层（智能体层）

**设计内容**：基于 LangChain 的智能体实现，负责与大模型交互

**职责**：
- 构造大模型提示词
- 调用大模型 API 进行推理
- 解析大模型返回结果
- 提供降级策略（当大模型不可用时使用规则匹配）

**实现功能**：
- **MatchingAgent**：接收源数据列和目标字段信息，调用大模型推理映射关系，解析 JSON 结果，带有降级匹配策略
- **FillAgent**：根据用户确认的映射规则，调用 BrowserFillerTool 驱动 Selenium 依次填充表单元素，对 select/radio 等控件先调用 OptionMatchingTool 进行值匹配

**关键文件**：[backend/agents/matching_agent.py](file:///d:/Essay/WebFill/backend/agents/matching_agent.py)、[backend/agents/fill_agent.py](file:///d:/Essay/WebFill/backend/agents/fill_agent.py)

#### 3.3.4 tools 层（工具层）

**设计内容**：具体功能实现工具，独立于业务逻辑

**职责**：
- 提供可复用的具体功能实现
- 封装外部服务调用（浏览器、文件系统等）
- 统一接口供上层调用

**实现功能**：
- **ExcelTool**：Excel 文件读取，支持 xlsx/xls 格式，双引擎支持（openpyxl/xlrd）
- **SQLFileTool**：SQL 文件解析，支持 CREATE TABLE 和 INSERT 语句提取
- **WebScraperTool**：网页抓取，使用 Selenium 处理动态页面
- **DataCleanerTool**：数据清洗，处理空值、格式转换、去重等
- **BrowserFillerTool**：浏览器自动化填充，支持 input/select/textarea/radio/checkbox 等控件
- **OptionMatchingTool**：选项值匹配，用于 select/radio 控件的值映射

**关键文件**：[backend/tools/excel_tool.py](file:///d:/Essay/WebFill/backend/tools/excel_tool.py)、[backend/tools/sql_file_tool.py](file:///d:/Essay/WebFill/backend/tools/sql_file_tool.py)、[backend/tools/web_scraper_tool.py](file:///d:/Essay/WebFill/backend/tools/web_scraper_tool.py)、[backend/tools/data_cleaner_tool.py](file:///d:/Essay/WebFill/backend/tools/data_cleaner_tool.py)、[backend/tools/browser_filler_tool.py](file:///d:/Essay/WebFill/backend/tools/browser_filler_tool.py)、[backend/tools/option_matching_tool.py](file:///d:/Essay/WebFill/backend/tools/option_matching_tool.py)

#### 3.3.5 models 层（数据模型层）

**设计内容**：基于 Pydantic 的数据模型定义

**职责**：
- 定义 API 请求和响应的数据结构
- 自动参数校验和类型转换
- 提供数据序列化和反序列化

**实现功能**：
- 定义 SourceInput、MappingRequest、FillRequest 等请求模型
- 定义 DataPreviewResponse、MappingResponse、FillResponse 等响应模型
- 自动校验请求参数的合法性

**关键文件**：[backend/models/schemas.py](file:///d:/Essay/WebFill/backend/models/schemas.py)

#### 3.3.6 utils 层（工具函数层）

**设计内容**：通用工具函数和辅助模块

**职责**：
- 提供跨模块复用的工具函数
- 配置管理（环境变量读取）
- 日志处理和异常处理
- 通用的辅助功能

**实现功能**：
- 环境变量加载和配置管理
- 日志记录和格式化
- 异常捕获和处理
- 通用数据处理函数

**关键文件**：[backend/utils/](file:///d:/Essay/WebFill/backend/utils/)（如存在）、[backend/.env](file:///d:/Essay/WebFill/backend/.env)

### 3.4 各层依赖关系

```
前端层 (fronted)
       │
       ▼
    api 层
       │
       ▼
 services 层
       │
   ┌───┴───┐
   ▼       ▼
agents 层 tools 层
   │       │
   └───┬───┘
       ▼
  models 层
       │
       ▼
  utils 层
```

---

## 四、核心功能实现

### 4.1 多源数据接入

**功能说明**：支持从 Excel、SQL 文件、网页表格三种来源读取数据

**实现流程**：

```
用户选择数据源 → 判断类型 → 调用对应工具 → 数据清洗 → 返回 DataFrame
```

**关键代码**：

```python
# 数据接入服务核心逻辑
async def load_source(self, source_input) -> pd.DataFrame:
    # 根据类型选择读取工具
    if source_input.type == "excel":
        df = self.excel_tool.read_from_bytes(source_input.content)
    elif source_input.type == "sql":
        df = self.sql_tool.parse_sql_file(sql_content)
    elif source_input.type == "url":
        df = await self._read_web_table(source_input.url)
    
    # 统一清洗流程
    df = self.cleaner.clean_dataframe(df)
    return df
```

**代码位置**：[backend/services/data_ingestion.py](file:///d:/Essay/WebFill/backend/services/data_ingestion.py)

### 4.2 智能字段映射

**功能说明**：基于大语言模型自动推荐源数据列与目标表单字段的映射关系

**实现流程**：

```
收集源数据信息 + 目标表单信息 → 构造提示词 → 调用大模型 → 解析 JSON 结果 → 返回映射推荐
```

**关键代码**：

```python
# 字段映射智能体核心逻辑
def get_mapping_recommendation(self, source_columns, source_sample, 
                                target_columns, target_labels):
    # 构造系统提示词
    self.system_prompt = """你是一个专业的数据映射专家..."""
    
    # 调用 LangChain Agent
    result = self.agent.invoke({
        "messages": [
            {"role": "system", "content": self.system_prompt},
            {"role": "user", "content": context}
        ]
    })
    
    # 解析 JSON 结果
    return self._extract_json(response_content)
```

**代码位置**：[backend/agents/matching_agent.py](file:///d:/Essay/WebFill/backend/agents/matching_agent.py)

### 4.3 自动表单填充

**功能说明**：根据用户确认的映射关系，自动填写目标网页表单

**实现流程**：

```
获取映射关系 → 转换数据格式 → 调用浏览器工具 → 逐个字段填充 → 完成填充
```

**关键代码**：

```python
# 数据填充服务核心逻辑
async def fill_target_form(self, source_df, mapping, target_url):
    # 列映射与重命名
    mapped_df = source_df[selected_cols].copy()
    mapped_df.rename(columns=rename_dict, inplace=True)
    
    # 格式转换和空值处理
    mapped_df = mapped_df.fillna('')
    
    # 调用填充智能体
    success, message = await self.fill_agent.fill(
        target_url=target_url,
        data_records=records,
        field_mapping=mapping
    )
```

**代码位置**：[backend/services/data_filling.py](file:///d:/Essay/WebFill/backend/services/data_filling.py)

---

## 五、系统执行流程

### 5.1 用户操作流程图

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        用户操作流程                                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌───────────┐ │
│  │  步骤1       │───▶│  步骤2       │───▶│  步骤3       │───▶│  步骤4   │ │
│  │ 上传源数据   │    │ 解析目标表单 │    │ 字段映射     │    │ 填充导出 │ │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘    └─────┬─────┘ │
│         │                  │                  │                  │       │
│         ▼                  ▼                  ▼                  ▼       │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌───────────┐ │
│  │选择数据源类型│    │输入目标URL  │    │智能推荐映射 │    │执行填充  │ │
│  │上传文件/URL │    │解析表单结构 │    │手动调整映射 │    │导出结果  │ │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘    └───────────┘ │
│         │                  │                  │                          │
│         ▼                  ▼                  ▼                          │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                  │
│  │Excel/SQL/URL│    │提取字段信息 │    │确认映射关系 │                  │
│  │→ DataFrame  │    │(label/name/ │    │→ 填充请求   │                  │
│  │→ 数据清洗   │    │ type/options)│    │            │                  │
│  └─────────────┘    └─────────────┘    └─────────────┘                  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.2 完整数据流图

| 步骤 | 前端操作 | API 端点 | 后端处理 | 涉及模块 |
|------|----------|----------|----------|----------|
| 1 | 上传源数据 | `POST /api/source/preview` | 读取文件 → 清洗 → 缓存 | DataIngestionService |
| 2 | 解析目标表单 | `GET /api/target/preview` | 抓取网页 → 解析表单结构 | WebScraperTool |
| 3 | 获取智能推荐 | `POST /api/mapping/recommend` | 构造提示词 → 调用大模型 → 解析结果 | MatchingAgent |
| 4 | 执行填充 | `POST /api/fill/execute` | 数据转换 → 浏览器自动化填充 | FillAgent + BrowserFillerTool |
| 5 | 导出结果 | `GET /api/export` | 读取缓存 → 生成文件 | pandas |

### 5.3 详细执行步骤说明

#### 步骤1：源数据接入

1. 用户选择数据源类型（Excel/SQL/URL）
2. 上传文件或输入 URL
3. 前端发送 POST 请求到 `/api/source/preview`
4. 后端根据类型调用对应工具读取数据：
   - **Excel**：[ExcelReadTool](file:///d:/Essay/WebFill/backend/tools/excel_tool.py)
   - **SQL**：[SQLFileTool](file:///d:/Essay/WebFill/backend/tools/sql_file_tool.py)
   - **URL**：[WebScraperTool](file:///d:/Essay/WebFill/backend/tools/web_scraper_tool.py)
5. 调用 [DataCleanerTool](file:///d:/Essay/WebFill/backend/tools/data_cleaner_tool.py) 清洗数据
6. 将数据缓存到内存，返回预览信息给前端

#### 步骤2：目标表单解析

1. 用户输入目标网页 URL
2. 前端发送 GET 请求到 `/api/target/preview`
3. 后端使用 Selenium 打开网页
4. 解析表单结构：
   - 提取所有 input、select、textarea 元素
   - 获取字段的 label、name、type、options
   - 处理 radio/checkbox 组
5. 返回字段信息给前端

#### 步骤3：智能字段映射

1. 用户点击"获取智能推荐映射"
2. 前端发送 POST 请求到 `/api/mapping/recommend`，包含：
   - 源数据列名、示例、类型
   - 目标字段名、标签、类型
3. 后端调用 [MatchingAgent](file:///d:/Essay/WebFill/backend/agents/matching_agent.py)：
   - 构造系统提示词
   - 调用阿里云百炼 API
   - 解析返回的 JSON 结果
4. 返回推荐的映射关系和置信度

#### 步骤4：表单填充

1. 用户确认映射关系（可手动调整）
2. 用户点击"确认填充到目标网页"
3. 前端发送 POST 请求到 `/api/fill/execute`
4. 后端调用 [DataFillingService](file:///d:/Essay/WebFill/backend/services/data_filling.py)：
   - 根据映射关系转换数据
   - 调用 [FillAgent](file:///d:/Essay/WebFill/backend/agents/fill_agent.py)
5. FillAgent 使用 [BrowserFillerTool](file:///d:/Essay/WebFill/backend/tools/browser_filler_tool.py)：
   - 打开 Firefox 浏览器
   - 访问目标网页
   - 逐个字段填充数据
   - 对于 select/radio，调用 [OptionMatchingTool](file:///d:/Essay/WebFill/backend/tools/option_matching_tool.py) 匹配选项

#### 步骤5：结果导出

1. 填充成功后，用户可选择导出格式（Excel/CSV）
2. 前端发送 GET 请求到 `/api/export`
3. 后端读取缓存的结果数据
4. 使用 pandas 生成 Excel 或 CSV 文件
5. 返回文件供用户下载

---

## 六、项目结构

```
WebFill/                              # 项目根目录
├── backend/                          # 后端代码
│   ├── main.py                       # FastAPI 应用入口
│   ├── api/                          # API 路由
│   │   └── endpoints.py              # 定义所有 API 端点
│   ├── services/                     # 业务服务层
│   │   ├── data_ingestion.py         # 数据接入服务
│   │   ├── field_matching.py         # 字段匹配服务
│   │   └── data_filling.py           # 数据填充服务
│   ├── agents/                       # 智能体层
│   │   ├── matching_agent.py         # 字段映射智能体
│   │   └── fill_agent.py             # 表单填充智能体
│   ├── tools/                        # 工具层
│   │   ├── excel_tool.py             # Excel 文件读取
│   │   ├── sql_file_tool.py          # SQL 文件解析
│   │   ├── web_scraper_tool.py       # 网页抓取
│   │   ├── data_cleaner_tool.py      # 数据清洗
│   │   ├── browser_filler_tool.py    # 浏览器填充
│   │   └── option_matching_tool.py   # 选项匹配
│   ├── models/                       # 数据模型
│   │   └── schemas.py                # Pydantic 模型定义
│   ├── .env                          # 环境变量配置
│   └── requirements.txt              # Python 依赖列表
├── fronted/                          # 前端代码
│   ├── index.html                    # 主页面
│   ├── main.js                       # 业务逻辑
│   └── styles.css                    # 样式文件
├── geckodriver.exe                   # Firefox 驱动程序
├── CODE_WIKI.md                      # 代码文档
└── README.md                         # 项目说明文档
```

---

## 七、运行说明

### 7.1 环境要求

- Python 3.10+
- Firefox 浏览器
- 阿里云百炼 API Key（需要在 [阿里云控制台](https://dashscope.aliyun.com/) 申请）

### 7.2 安装依赖

```bash
cd backend
pip install -r requirements.txt
```

### 7.3 配置环境变量

在 `backend/.env` 文件中配置：

```ini
# 阿里云百炼 API Key（替换为您自己的）
DASHSCOPE_API_KEY=your_api_key_here

# 模型名称（默认 qwen-turbo）
QWEN_MODEL_NAME=qwen-turbo

# API 基础 URL
QWEN_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1
```

### 7.4 启动服务

```bash
cd backend
python main.py
```

### 7.5 访问系统

1. 启动后端服务后，访问 API 文档：http://localhost:8000/docs
2. 打开前端页面：直接在浏览器中打开 `fronted/index.html`

---

## 八、毕业答辩常见问题准备

### Q1：系统的核心创新点是什么？

**回答要点**：
- 将大语言模型引入字段映射，实现智能匹配，无需人工干预
- 支持多源数据接入（Excel/SQL/网页），统一处理
- 结合浏览器自动化技术，实现端到端自动填充

### Q2：为什么选择大语言模型进行字段匹配？

**回答要点**：
- 语义理解能力：可以理解字段名称的含义（如"姓名"和"name"是同一个意思）
- 灵活性：支持同义词、多语言、缩写等复杂情况
- 智能推理：可以根据示例数据推断字段含义
- 可扩展性：不需要手动编写大量规则

### Q3：系统的技术架构是怎样的？

**回答要点**：
- 采用分层架构：表现层 → API 层 → 业务服务层 → 智能体层 → 工具层
- 各层职责清晰，解耦性好
- 使用 FastAPI + Vue 3 实现前后端分离

### Q4：如何保证系统的稳定性和容错性？

**回答要点**：
- 大模型调用有降级策略（当 API 不可用时使用规则匹配）
- 所有外部调用都有异常处理
- 文件读取支持多种引擎（openpyxl/xlrd）
- 数据清洗保证数据质量

### Q5：系统的性能如何？

**回答要点**：
- 使用异步框架（FastAPI + async/await）
- 数据处理使用 pandas，效率高
- 浏览器自动化是瓶颈，但限制单次最多处理 100 条记录

### Q6：未来可以如何扩展？

**回答要点**：
- 支持更多数据源（数据库直连、CSV 等）
- 添加数据验证和格式转换规则
- 支持多表单批量填充
- 添加日志和监控功能
- 支持用户自定义映射规则

---

## 九、总结

本系统实现了一个基于大语言模型的智能表单映射填充系统，主要特点：

1. **智能化**：利用大模型实现字段智能匹配
2. **自动化**：自动读取数据、解析表单、填充数据
3. **多源支持**：支持 Excel、SQL、网页三种数据源
4. **易用性**：简单的 4 步操作流程

该系统可以显著提高表单填写效率，减少重复劳动，具有实际应用价值。
