# day05

---
### 本地模型

ollama的默认端口为11434

本地模型接口地址：http://localhost:11434/api/chat
	../api/chat 该端点专门为模拟聊天场景设计，具备处理上下文对话、跟踪对话历史记录、理解对话上下文的作用，主要用于构建交互式聊天应用，如智能客服、聊天机器人等
	基本参数：
		model（必需）：模型名称
		messages：聊天消息，可用于保留聊天记录
		messages对象的参数：
			role：消息的角色，可以是system（系统）、user（用户）、assistant（助手）或tool（工具）
			content：消息的内容

```
获取模型url配置地址2时，Value需要选择名为springframework的jar包
```
建立本地模型：
1. 在yml配置中完成对本地deepseek模型和百度人脸识别APi的调用
```Java
deepseek:  
  api:  
    url: http:localhost:11434/api/chat  
    model: deepseek-r1:7b  
  
baidu:  
  api:  
    appid: 7025056  
    apikey: Qv1h2VgziPWcEhIDzEApUCuX  
    secretkey: 0dz9b9CjneqJ9FdY3DAPHlnt6fN1pSko
```
2. 实现Controller层：
```Java
@RestController  
public class ValueController {  
  
  
    @Value("${deepseek.api.url}") // 获取yml配置中的 url    
    private String url;  
  
    private ValueController(){  
        System.out.println("url:" + url);  
    }  
  
    @GetMapping("url")  
    private String getUrl(){  
        return url;  
    }  
  
}
```
3. 实现Service层和ServiceImp实现层
```Java
// Service 层
public interface ChatService{  
    public String chat (String question) throws JsonProcessingException;  
}
```

```Java
// ServiceImp层
@Service // 实例化  
public class ChatServiceImp implements ChatService {  
    @Value("${deepseek.api.model}")  
    private String model;  
  
    @Value("${deepseek.api.url}")  
    private String url;  
  
    @Override  
    public String chat(String question) throws JsonProcessingException {  
        // 此项业务方法处理逻辑：把从本地页面获取的内容发送给deepseek，然后把deepseek的信息返回给项目的前端页面  
        ChatRequest.Message message = new ChatRequest.Message();  
        message.setRole("assistant");  
        message.setContent(question);  
  
        ChatRequest chatRequest  = new ChatRequest();  
        chatRequest.setModel(model);  
        chatRequest.setMessages(Collections.singletonList(message)); // 参考Javase文档集合篇  
  
        // 把数据发送给deepseek  
        // RestTemplate是Springboot提供的一个工具  
        RestTemplate restTemplate = new RestTemplate(); // 创建 http请求对象  
  
        Map<Object, String> maps = restTemplate.postForObject(url, chatRequest, Map.class); // 发送的是post请求  
  
        ObjectMapper objectMapper = new ObjectMapper(); // 处理json数据  
  
        String json = objectMapper.writeValueAsString(maps);  
  
        System.out.println(json);  
  
        return json;  
  
    }  
}
```


若使用Postman测试后端接口连接，则发送数据格式为：
```
model":"deepseek-r1:1.5b","messages":[{"role":"user","content":"{\"question\":\"昆明\"}"}],"stream":false
```


### Ollama命令

1. ollama list：查看已下载的模型列表
2. ollama pull <模型名>：下载模型（如："llama3",deepseek）
3. ollama run <模型名>：运行指定模型（进入交互对话）
4. ollama rm <模型名>：删除本地模型
5. ollama help：查看帮助文档

