# WebFill 智能表单映射填充系统 - Code Wiki

## 1. 项目概述

### 1.1 项目简介

WebFill 是一个多源 Web 表格自动填写系统，通过整合 LangChain Agent、Selenium 浏览器自动化和阿里云百炼大模型，实现从多种数据源（Excel、SQL、网页表格）自动提取数据，并将数据智能映射填充到目标网页表单的功能。

### 1.2 技术栈

| 层级 | 技术选型 |
|------|----------|
| 前端 | Vue 3 Composition API + Axios |
| 后端 | FastAPI + Uvicorn |
| 数据处理 | Pandas + OpenPyXL |
| AI 智能体 | LangChain + 阿里云百炼 (DashScope) |
| 浏览器自动化 | Selenium + Firefox (geckodriver) |
| 数据库 | MySQL (通过 SQLAlchemy + PyMySQL) |
| 其他 | BeautifulSoup4, lxml, python-dotenv |

### 1.3 项目结构

```
d:\Essay\WebFill/
├── backend/                          # 后端服务目录
│   ├── main.py                       # FastAPI 应用入口
│   ├── requirements.txt              # Python 依赖清单
│   ├── .env                          # 环境变量配置
│   ├── api/
│   │   └── endpoints.py              # API 路由定义
│   ├── models/
│   │   └── schemas.py                # Pydantic 数据模型
│   ├── agents/                       # AI 智能体模块
│   │   ├── fill_agent.py            # 表单填充智能体
│   │   └── matching_agent.py        # 字段匹配智能体
│   ├── services/                     # 业务服务层
│   │   ├── data_ingestion.py        # 数据接入服务
│   │   ├── field_matching.py         # 字段匹配服务
│   │   └── data_filling.py           # 数据填充服务
│   ├── tools/                        # 工具函数库
│   │   ├── excel_tool.py             # Excel 读取工具
│   │   ├── sql_file_tool.py          # SQL 文件解析工具
│   │   ├── web_scraper_tool.py       # 网页抓取与表单解析工具
│   │   ├── data_cleaner_tool.py      # 数据清洗工具
│   │   ├── browser_filler_tool.py    # 浏览器表单填充工具
│   │   ├── field_matching_tools.py   # 字段匹配 LangChain 工具
│   │   └── option_matching_tool.py   # 选项匹配工具
│   └── utils/
│       └── helpers.py                # 通用辅助函数
├── frontend/                         # 前端页面目录
│   ├── index.html                    # 主页面
│   ├── main.js                       # Vue 3 业务逻辑
│   └── styles.css                    # 样式表
├── geckodriver.exe                   # Firefox WebDriver
└── readme.txt                        # 项目说明文档
```

---

## 2. 系统架构

### 2.1 整体架构

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   前端 (Vue 3)  │────▶│  后端 (FastAPI)  │────▶│  LangChain Agent │
│   localhost:xxx │     │  localhost:8000  │     │    (百炼大模型)   │
└─────────────────┘     └────────┬─────────┘     └─────────────────┘
                                 │
                    ┌────────────┼────────────┐
                    ▼            ▼            ▼
              ┌──────────┐ ┌──────────┐ ┌──────────┐
              │ Services │ │  Tools   │ │ Selenium  │
              │  服务层   │ │  工具层  │ │ 浏览器自动化│
              └──────────┘ └──────────┘ └──────────┘
```

### 2.2 数据流

1. **源数据加载** → 用户上传 Excel/SQL 文件或输入网页 URL
2. **目标表单解析** → 系统自动提取目标网页的表单字段结构
3. **智能映射推荐** → LangChain Agent 调用大模型推理字段对应关系
4. **手动映射调整** → 用户可修改或确认映射关系
5. **浏览器填充执行** → Selenium 控制 Firefox 自动填写表单
6. **结果导出** → 生成 Excel/CSV 文件

---

## 3. API 接口规范

### 3.1 核心端点

| 端点 | 方法 | 功能 | 请求体/参数 |
|------|------|------|-------------|
| `/api/source/preview` | POST | 预览源数据 | `type`, `file`/`url` |
| `/api/target/preview` | GET | 解析目标表单 | `target_url` |
| `/api/mapping/recommend` | POST | 获取智能映射推荐 | `MappingRequest` |
| `/api/fill/execute` | POST | 执行表单填充 | `FillRequest` |
| `/api/export` | GET | 导出结果文件 | `format` (excel/csv) |

### 3.2 数据模型

详见 [models/schemas.py](file:///d:/Essay/WebFill/backend/models/schemas.py)：

- `SourceInput`: 源数据输入
- `MappingRequest`: 映射请求
- `FillRequest`: 填充请求
- `DataPreviewResponse`: 数据预览响应
- `TargetPreviewResponse`: 目标表单预览响应
- `MappingRecommendation`: 映射推荐结果
- `FillResultResponse`: 填充结果响应

---

## 4. 核心模块详解

### 4.1 API 层 (api/)

#### endpoints.py

API 路由分发中心，处理所有 HTTP 请求。

**主要函数：**

```python
# 预览源数据
@router.post("/source/preview", response_model=DataPreviewResponse)
async def preview_source_data(type: str, file: UploadFile, url: str)

# 预览目标表单
@router.get("/target/preview", response_model=TargetPreviewResponse)
async def preview_target_form(target_url: str)

# 获取映射推荐
@router.post("/mapping/recommend", response_model=MappingRecommendation)
async def recommend_mapping(request: MappingRequest)

# 执行表单填充
@router.post("/fill/execute", response_model=FillResultResponse)
async def execute_fill(request: FillRequest)

# 导出结果
@router.get("/export")
async def export_result(format: str = "excel")
```

### 4.2 智能体层 (agents/)

#### matching_agent.py

基于 LangChain 的字段匹配智能体，调用百炼大模型进行语义级别的字段映射。

**核心逻辑：**
1. 系统提示词定义任务：分析源数据列与目标表单字段的语义对应关系
2. Agent 调用 `analyze_source_columns` 工具获取源数据详情
3. Agent 调用 `analyze_target_fields` 工具获取目标字段详情
4. 大模型推理并返回 JSON 格式的映射建议
5. 降级策略：Agent 调用失败时使用规则匹配

**关键方法：**

```python
class MatchingAgent:
    def __init__(self)
    def get_mapping_recommendation(
        source_columns: List[str],
        source_sample: List[Dict],
        source_types: Dict[str, str],
        target_columns: List[str],
        target_labels: Dict[str, str],
        target_types: Dict[str, str],
        target_sample: List[Dict]
    ) -> Dict[str, Any]
    def _extract_json(content: str, ...) -> Dict
    def _fallback_matching(...) -> Dict  # 降级规则匹配
```

#### fill_agent.py

表单填充智能体，协调浏览器自动化工具完成批量填充。

**核心逻辑：**
1. 校验数据记录数量（限制 100 条以内）
2. 遍历每条记录，根据字段映射提取数据
3. 调用 `BrowserFillerTool` 执行单条记录填充
4. 汇总填充结果

```python
class FillAgent:
    def __init__(self)
    async def fill(
        target_url: str,
        data_records: list,
        field_mapping: dict,
        fields_info: list = None
    ) -> tuple[bool, str, int]
```

### 4.3 服务层 (services/)

#### data_ingestion.py

数据接入与缓存管理服务。

**核心功能：**
- 支持三种数据源：Excel、SQL、网页 URL
- 数据清洗处理
- 内存缓存（`_cache`）保存 DataFrame

```python
class DataIngestionService:
    def cache_dataframe(key: str, df: pd.DataFrame)
    def get_cached_dataframe(key: str) -> Optional[pd.DataFrame]
    async def load_source(source_input: SourceInput) -> pd.DataFrame
    async def parse_target_form(url: str) -> Dict[str, Any]
```

#### field_matching.py

字段匹配服务，封装 MatchingAgent 的调用。

```python
class FieldMatchingService:
    async def get_recommendation(...) -> Dict[str, Any]
```

#### data_filling.py

数据填充服务，负责数据转换和协调 FillAgent。

**处理流程：**
1. 列映射重命名
2. 格式转换（日期处理）
3. 空值处理
4. 调用填充智能体
5. 保存结果 DataFrame

```python
class DataFillingService:
    async def fill_target_form(
        source_df: pd.DataFrame,
        mapping: Dict[str, str],
        target_url: str,
        target_labels: Dict[str, str],
        target_fields_info: List[Dict],
        options: Dict
    ) -> Dict[str, Any]
    def get_result_dataframe() -> Optional[pd.DataFrame]
```

### 4.4 工具层 (tools/)

#### excel_tool.py

Excel 文件读取工具，支持 .xlsx 和 .xls 格式。

```python
class ExcelReadTool:
    def read_from_bytes(content: bytes, sheet_name: Union[str, int] = 0) -> pd.DataFrame
    def read_from_file(file_path: str, sheet_name: Union[str, int] = 0) -> pd.DataFrame
    def get_sheet_names(content: bytes) -> List[str]
```

#### sql_file_tool.py

SQL 文件解析工具，解析 CREATE TABLE 和 INSERT 语句。

**支持格式：**
- MySQL/PostgreSQL 导出的 SQL 文件
- 反引号包裹的标识符
- 多条 INSERT 语句
- 注释移除（`--`、`#`、`/* */`）

```python
class SQLFileTool:
    def parse_sql_file(sql_content: str) -> pd.DataFrame
    def _remove_comments(sql: str) -> str
    def _parse_create_table(stmt: str) -> Tuple[str, List[str]]
    def _parse_insert_statement(stmt: str, expected_cols: int) -> List[List]
    def _split_row_values(text: str) -> List
```

#### web_scraper_tool.py

网页抓取与表单解析工具，支持静态和动态页面。

**核心功能：**
- 提取网页表格数据
- 解析表单字段结构（label、name、type、options）
- 标签匹配策略：for/id > 父级 label > aria-label > placeholder > title

```python
class WebScraperTool:
    async def extract_tables(url: str) -> List[pd.DataFrame]
    async def extract_form_structure(url: str) -> Dict[str, Any]
    def _clean_label(text: str) -> str
```

#### data_cleaner_tool.py

数据清洗工具，执行标准化清洗操作。

**清洗步骤：**
1. 清理列名特殊字符
2. 智能跳过备注表头行
3. 清理字符串列
4. 删除空行
5. 删除重复行

```python
class DataCleanerTool:
    def clean_dataframe(df: pd.DataFrame) -> pd.DataFrame
    def _clean_column_names(columns: pd.Index) -> pd.Index
    def _skip_header_rows(df: pd.DataFrame) -> pd.DataFrame
    def _clean_string_columns(df: pd.DataFrame) -> pd.DataFrame
```

#### browser_filler_tool.py

浏览器表单填充工具，使用 Selenium 控制 Firefox。

**定位策略：**
1. `By.NAME`
2. `By.ID`
3. `placeholder` 属性
4. `label` 文本关联

**支持字段类型：**
- 普通文本输入框 (`input[type="text"]`)
- 文本域 (`textarea`)
- 下拉框 (`select`)
- 单选按钮 (`input[type="radio"]`)
- 复选框 (`input[type="checkbox"]`)

```python
class BrowserFillerTool:
    async def fill_single_record(
        target_url: str,
        data: Dict[str, str],
        fields_info: Optional[List[Dict]]
    ) -> Tuple[bool, str]
    def _locate_element(driver, field_name)
    def _fill_text_or_select(element, value: str)
    def _click_radio_checkbox(driver, field_name, value, input_type)
```

#### field_matching_tools.py

LangChain Agent 工具集，定义 `@tool` 装饰的工具函数。

```python
@tool
def analyze_source_columns(
    source_columns: List[str],
    source_sample: List[Dict],
    source_types: Dict[str, str]
) -> str

@tool
def analyze_target_fields(
    target_columns: List[str],
    target_labels: Dict[str, str],
    target_types: Dict[str, str],
    target_sample: List[Dict]
) -> str
```

#### option_matching_tool.py

选项值匹配工具，支持下拉框、单选、复选框的智能匹配。

**匹配策略：**
1. 优先调用百炼大模型直接判断
2. 降级策略：生成同义词 + 模糊匹配

```python
class OptionMatchingTool:
    def match_value_to_options(
        value: str,
        field_label: str,
        options: List[dict],
        multi: bool = False
    ) -> List[str]
    def _expand_synonyms_sync(value: str, field_label: str) -> List[str]
    def _fallback_match(...) -> List[str]
```

### 4.5 工具函数 (utils/)

#### helpers.py

通用辅助函数。

```python
def random_delay(min_seconds: float = 0.5, max_seconds: float = 2.0)
def safe_find_element(driver, by, value, timeout=10)
```

---

## 5. 前端实现

### 5.1 技术架构

基于 Vue 3 Composition API 构建的单页面应用。

### 5.2 核心状态

```javascript
const sourceType = ref('excel')           // 数据源类型
const sourcePreview = reactive({...})      // 源数据预览
const targetFieldsInfo = ref([])           // 目标表单字段
const mappingPairs = ref([])               // 映射关系对
const fillResult = ref(null)               // 填充结果
```

### 5.3 工作流程

1. **步骤 1**：上传源数据（Excel/SQL/URL）
2. **步骤 2**：解析目标网页表单
3. **步骤 3**：获取智能映射推荐 → 手动调整 → 确认填充
4. **步骤 4**：导出结果文件

---

## 6. 依赖关系

### 6.1 Python 依赖

```
fastapi              # Web 框架
uvicorn[standard]   # ASGI 服务器
pandas              # 数据处理
openpyxl            # Excel 读取
sqlalchemy          # ORM
pymysql             # MySQL 驱动
lxml                # XML/HTML 解析
beautifulsoup4      # 网页解析
selenium            # 浏览器自动化
langchain>=1.0     # AI Agent 框架
langchain-openai    # OpenAI 兼容接口
dashscope           # 阿里云百炼 SDK
python-dotenv       # 环境变量管理
sqlparse            # SQL 解析
python-multipart    # 文件上传
```

### 6.2 环境变量配置 (.env)

```bash
DASHSCOPE_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx    # 阿里云百炼 API Key
QWEN_MODEL_NAME=qwen-turbo                  # 模型名称
QWEN_BASE_URL=https://dashscope.aliyuncs.com/compatible-mode/v1  # API 地址
```

---

## 7. 项目运行指南

### 7.1 环境准备

```bash
# 1. 安装 Python 3.10+
# 2. 进入 backend 目录安装依赖
cd backend
pip install -r requirements.txt

# 3. 配置 .env 文件
# 填入阿里云百炼 API Key

# 4. 安装 Firefox 浏览器
# 5. 下载 geckodriver.exe（与 Firefox 版本匹配）
#    放置于项目根目录 d:\Essay\WebFill\
```

### 7.2 启动服务

```bash
# 启动后端服务
cd backend
uvicorn main:app --reload --port 8000

# 访问 API 文档
# http://localhost:8000/docs

# 打开前端页面
# 直接用浏览器打开 frontend/index.html
```

### 7.3 注意事项

- 填充时会打开 Firefox 窗口，请勿关闭主窗口
- 批量填充最多支持 100 条记录
- 若大模型 API 不可用，字段映射会降级为规则匹配
- 目标网页加载较慢时，已设置超时补偿

---

## 8. 扩展建议

1. **新增 Agent 工具**：可增加数据清洗建议、转换规则推理等工具
2. **浏览器会话复用**：提升批量填充效率
3. **更多数据源**：支持 JSON、REST API 等
4. **映射持久化**：保存历史映射配置
5. **批量任务队列**：支持后台批量处理

---

## 9. 文件索引

| 文件路径 | 说明 |
|----------|------|
| [backend/main.py](file:///d:/Essay/WebFill/backend/main.py) | FastAPI 应用入口 |
| [backend/api/endpoints.py](file:///d:/Essay/WebFill/backend/api/endpoints.py) | API 路由定义 |
| [backend/models/schemas.py](file:///d:/Essay/WebFill/backend/models/schemas.py) | 数据模型 |
| [backend/agents/matching_agent.py](file:///d:/Essay/WebFill/backend/agents/matching_agent.py) | 字段匹配 Agent |
| [backend/agents/fill_agent.py](file:///d:/Essay/WebFill/backend/agents/fill_agent.py) | 表单填充 Agent |
| [backend/services/data_ingestion.py](file:///d:/Essay/WebFill/backend/services/data_ingestion.py) | 数据接入服务 |
| [backend/services/field_matching.py](file:///d:/Essay/WebFill/backend/services/field_matching.py) | 字段匹配服务 |
| [backend/services/data_filling.py](file:///d:/Essay/WebFill/backend/services/data_filling.py) | 数据填充服务 |
| [backend/tools/excel_tool.py](file:///d:/Essay/WebFill/backend/tools/excel_tool.py) | Excel 读取工具 |
| [backend/tools/sql_file_tool.py](file:///d:/Essay/WebFill/backend/tools/sql_file_tool.py) | SQL 文件解析工具 |
| [backend/tools/web_scraper_tool.py](file:///d:/Essay/WebFill/backend/tools/web_scraper_tool.py) | 网页抓取工具 |
| [backend/tools/data_cleaner_tool.py](file:///d:/Essay/WebFill/backend/tools/data_cleaner_tool.py) | 数据清洗工具 |
| [backend/tools/browser_filler_tool.py](file:///d:/Essay/WebFill/backend/tools/browser_filler_tool.py) | 浏览器填充工具 |
| [backend/tools/field_matching_tools.py](file:///d:/Essay/WebFill/backend/tools/field_matching_tools.py) | LangChain 工具 |
| [backend/tools/option_matching_tool.py](file:///d:/Essay/WebFill/backend/tools/option_matching_tool.py) | 选项匹配工具 |
| [frontend/index.html](file:///d:/Essay/WebFill/fronted/index.html) | 前端页面 |
| [frontend/main.js](file:///d:/Essay/WebFill/fronted/main.js) | Vue 业务逻辑 |
