# OpenAI库的使用
### 一、调用模型（向模型提问）

```python
# 这两行在 Python 虚拟机中执行时，字节码完全一致
response: ChatCompletion = client.chat.completions.create(...)
response = client.chat.completions.create(...)
```

**在运行时这两行代码完全等同**，`: ChatCompletion` 只是给开发工具看的"注释"，Python 解释器会直接忽略它。仅表明`response`是一个ChatCompletion对象
![](assets/Pasted%20image%2020260317134935.png)
client.chat.completions.create ：创建一个ChatCompletion对象 --> 向指定模型提问
主要有两个参数：
1. `model`：选择所用的模型，如代码中的qwen3-max
2. `messages`：提供给模型的消息
	- 类型：list列表类型，可以包含字典消息（每个元素都可以是字典类型）
	- 每个字典包含2个key：
		1. role：角色
			- system角色：设定助手的整体行为、角色和规则，为对话提供上下文框架（如指定助手身份、回答风格、核心要求等），是全局的背景设定，影响后续所有交互
			- assistant角色：代表AI助手的回答，可以在代码中设定
			- user角色：代表用户，发送问题、指令或需求
		2. content：内容
#### 示例

```python
from openai import OpenAI  
  
# 1. 获取client对象，OpenAI类对象  
client = OpenAI(  
    base_url = "https://dashscope.aliyuncs.com/compatible-mode/v1"  
)  
  
# 2. 调用模型  
response = client.chat.completions.create(  
    model="qwen3-max-2026-01-23",  
    messages=[  
        {"role": "system", "content": "你是一个Python编程专家，从不说废话"},  
        {"role": "assistant", "content": "好的，我的一个编程专家，您有什么问题吗？"},  
        {"role": "user", "content": "请写一个Python程序，输出1~10的所有数字"}  
    ])  
print(response)  
  
# 3. 处理结果  
print(response.choices[0].message.content) # 根据返回的response结构，提取出准确的回复内容块
```

```python
# 以下是代码执行后的输出结果
ChatCompletion(id='chatcmpl-0eec4a9b-b455-9a1c-a65d-3b284c6bd038', choices=[Choice(finish_reason='stop', index=0, logprobs=None, message=ChatCompletionMessage(content='```python\nfor i in range(1, 11):\n    print(i)\n```', refusal=None, role='assistant', annotations=None, audio=None, function_call=None, tool_calls=None))], created=1773728336, model='qwen3-max-2026-01-23', object='chat.completion', service_tier=None, system_fingerprint=None, usage=CompletionUsage(completion_tokens=19, prompt_tokens=57, total_tokens=76, completion_tokens_details=None, prompt_tokens_details=None))

python
for i in range(1, 11):
    print(i)
```

### 二、OpenAI库的流式输出

![](assets/Pasted%20image%2020260317143042.png)

可以设定输出结果为stream模式（流式输出模式），获得更好的使用体验。
开启流式输出主要步骤有两步：
1. 在`client.chat.completions.create()`调用模型的时候设定参数：`stream = True`
2. for 循环 response 对象，并在循环中输出内容
#### 示例

```python
from openai import OpenAI  
  
# 1. 获取client对象，OpenAI类对象  
client = OpenAI(  
    base_url = "https://dashscope.aliyuncs.com/compatible-mode/v1"  
)  
  
# 2. 调用模型  
response = client.chat.completions.create(  
    model="qwen3-max-2026-01-23",  
    messages=[  
        {"role": "system", "content": "你是一个Python编程专家，且会详细介绍讲解代码内容"},  
        {"role": "assistant", "content": "好的，我的一个编程专家，您有什么问题吗？"},  
        {"role": "user", "content": "请写一个Python程序，输出1~10的所有数字"}  
    ],    stream=True  
)  
  
# 3. 处理结果  
# print(response.choices[0].message.content) # 根据返回的response结构，提取出准确的回复内容块  
for chunk in response:  
    print(  
        chunk.choices[0].delta.content,  # 此处有变化
        end=" ",   # 每一段输出之间以空格为间隔  
        flush=True  # 立刻刷新缓冲区（因为在某些系统中输出会被存放到缓冲区中，而不是立刻显示）  
    )
```

>需要注意的是，使用流式输出后，得用到for循环，且输出结构从choice\[0\].message.content变为了choice\[0\].delta.content
>`delta.content` 包含的是**增量内容**（每次生成的一小段文本）

输出结果：
	![](assets/Pasted%20image%2020260317143533.png)

### 三、OpenAI库附带历史消息调用模型

调用模型传入的参数messages，其要求是list对象，即表明其支持很多消息在内。我们可以基于此，将历史消息填入，让模型知晓对话的上下文，更好的回答问题
`messages` 数组本质上是一个"**剧本**" ，你可以自由编排 `user` 和 `assistant` 的对话历史，让 AI "误以为"这些对话真的发生过。

![](assets/Pasted%20image%2020260317150346.png)

#### 示例

```python
from openai import OpenAI  
  
# 1. 获取client对象，OpenAI类对象  
client = OpenAI(  
    base_url = "https://dashscope.aliyuncs.com/compatible-mode/v1"  
)  
  
# 2. 调用模型  
response = client.chat.completions.create(  
    model="qwen3-max-2026-01-23",  
    messages=[  
        {"role": "system", "content": "你是一个AI助理，从不说废话"},  
        {"role": "user", "content": "小明家里有3条狗"},  
        {"role": "assistant", "content": "好的"},  
        {"role": "user", "content": "小红家里有2条狗"},  
        {"role": "assistant", "content": "好的"}, # 以上的信息都是上下文信息，大模型会根据这些信息进行回答  
        {"role": "user", "content": "他们一共有多少条狗"}  # 大模型最终回答的是这个问题  
    ],  
    stream=True  
)  
  
# 3. 处理结果  
# print(response.choices[0].message.content) # 根据返回的response结构，提取出准确的回复内容块  
for chunk in response:  
    print(  
        chunk.choices[0].delta.content,  
        end=" ",   # 每一段输出之间以空格为间隔  
        flush=True  # 立刻刷新缓冲区（因为在某些系统中输出会被存放到缓冲区中，而不是立刻显示）  
    )
```

输出：
![](assets/Pasted%20image%2020260317151543.png)


#### 后续改进

由于当前使用的**OpenAI**历史消息是**一次性**的，如果是生产系统，则可以将消息保存到文件、数据库等持久化工具中，需要的时候再提取使用

后续学习的**LangChain**库中会学习**短期记忆**和**长期记忆**的使用方法