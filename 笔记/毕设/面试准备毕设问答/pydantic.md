系统中**使用了 Pydantic，但没有使用 `typing.Annotated`**。下面分别说明。

---

### 一、Pydantic 的使用

Pydantic 是 FastAPI 的数据校验和序列化核心。系统在 **后端 API 接口层** 和 **数据模型定义** 中大量使用。

#### 1. 位置：`backend/models/schemas.py`
这个文件定义了所有请求和响应的 Pydantic 模型。例如：
```python
from pydantic import BaseModel

class SourceInput(BaseModel):
    type: str
    filename: Optional[str] = None
    content: Optional[bytes] = None
    url: Optional[str] = None

class FillRequest(BaseModel):
    mapping: Dict[str, str]
    target_labels: Dict[str, str]
    target_fields_info: Optional[List[Dict[str, Any]]] = None
    target_url: str
    fill_options: Optional[Dict] = {}
```

**做了什么**：
- **数据校验**：当前端发来 JSON 请求时，FastAPI 会用这些模型自动校验字段类型、是否必填、格式是否正确。如果格式不对，直接返回 422 错误，不需要手动写校验逻辑。
- **自动文档**：FastAPI 基于这些模型生成 Swagger UI 中的请求/响应示例。
- **类型安全**：在整个后端代码中，传入的参数都是已经校验过的 Pydantic 对象，IDE 能提供类型提示和自动补全。

#### 2. 位置：API 路由层（`backend/api/endpoints.py`）
```python
@router.post("/mapping/recommend", response_model=MappingRecommendation)
async def recommend_mapping(request: MappingRequest):
    ...
```
`request: MappingRequest` 表示自动将前端 JSON 解析为 MappingRequest 对象。`response_model=MappingRecommendation` 表示返回的数据会自动序列化为 JSON，并确保结构符合 MappingRecommendation 模型。

#### 3. 其他 Pydantic 模型
系统中还有 `DataPreviewResponse`、`TargetPreviewResponse`、`FillResultResponse` 等，都用于标准化 API 的输出格式。

**一句话总结**：Pydantic 在系统中负责**接口契约**——定义数据长什么样，自动校验和序列化，避免手动解析 JSON 和编写重复的校验代码。

---

### 二、`typing.Annotated` 是否使用？

**没有使用**。`Annotated` 是 Python 3.9+ 引入的类型标注机制，通常和 Pydantic 配合可以给字段添加额外元数据（如描述、校验规则），例如：
```python
from typing import Annotated
from pydantic import Field

class SomeModel(BaseModel):
    name: Annotated[str, Field(description="用户姓名")]
```

但在本系统中，所有 Pydantic 模型都直接使用 `Field()` 或简单的类型标注（如 `str`、`int`、`Optional[str]`），没有使用 `Annotated`。这是因为系统场景简单，不需要在每个字段上添加额外描述，保持代码简洁即可。若未来需要更精细的字段说明或自定义校验，可以引入 `Annotated` 结合 Pydantic 的 `Field` 来增强表达能力。

---

### 三、面试回答模板

> **面试官**：你的系统里有用到 Pydantic 吗？做了什么？

> **你**：用了。Pydantic 是 FastAPI 的数据校验和序列化核心。我在 `models/schemas.py` 里定义了所有请求和响应的模型，比如 `SourceInput` 用于接收前端上传的文件参数，`MappingRecommendation` 用于返回 Agent 的映射结果。这些模型会自动校验前端传来的数据格式，如果字段类型错误或缺少必填项，FastAPI 会直接返回 422，不需要我手动写校验逻辑。同时也自动生成了 API 文档，方便前端联调。整个后端的数据流转都是基于这些 Pydantic 模型进行的，保证类型安全和接口一致性。

> **面试官**：有没有用到 `Annotated`？

> **你**：没有。`Annotated` 可以用来给 Pydantic 字段添加额外元数据，但我的系统字段比较简单，直接定义类型就够用了，所以没有引入。如果以后需要更丰富的字段描述或自定义校验，可以结合 `Annotated` 和 `Field` 来做。