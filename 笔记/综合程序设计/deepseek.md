**/api/generate**

- 它是一个相对基础的文本生成端点，主要用于根据给定的提示信息生成一段连续的文本。这个端点会基于输入的提示，按照模型的语言生成能力输出一段完整的内容，更侧重于单纯的文本生成任务
- 生成过程不依赖于上下文的历史对话信息，每次请求都是独立的，模型仅依据当前输入的提示进行文本生成
- 适用于需要一次性生成一段特定主题文本的场景，如撰写文章、
- 生成故事、生成摘要等。当你只需要根据一个简单的提示获取一段完整的文本内容时，使用 `api/generate` 会更加方便，例如，你需要生成一篇关于旅游景点的介绍文章，只需要提供景点名称作为提示，就可以使用该端点获取相应的文章内容
- 基本参数：
  - model（必需） : 模型名称
  - prompt：用于指定要生成文本的起始信息
  - stream:是否流时输出



**/api/chat**

- 该端点专为模拟聊天场景设计，具备处理对话上下文的能力。它可以跟踪对话的历史记录，理解对话的上下文信息，从而生成更符合对话逻辑和连贯性的回复
- 更注重模拟真实的人机对话交互，能够根据历史对话和当前输入生成合适的回应，适用于构建聊天机器人等交互式应用
- 主要用于构建交互式的聊天应用，如智能客服、聊天机器人等。在这些场景中，用户与系统进行多轮对话，需要系统能够根据历史对话内容理解用户意图并做出合适的回应，比如，在一个在线客服系统中，用户可以不断提出问题，系统使用 `api/chat` 端点根据历史对话和当前问题生成相应的解答，为用户提供连贯的服务
- 基本参数：
  - model（必需） : 模型名称
  - messages：聊天消息，可用于保留聊天记录
- messages 对象的参数：
  - role：消息的角色，可以是 system（系统）、user（用户）、assistant（助手）或 tool（工具）
  - content：消息的内容



## 3 基于springboot+vue-cli+deepSeek聊天机器人案例

### 3.1 springboot实现步骤 

1 添加依赖

```xml
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.2.RELEASE</version>
    </parent>
    <dependencies>
        <!--spring boot web集成依赖  -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--data注解所需依赖-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        </dependencies>
```

2 在application配置文件中配置访问deepseek

``` properties
# DeepSeek API 配置
deepseek.api.url=http://localhost:11434/api/chat
deepseek.api.model=deepseek-r1:1.5b
```

3 创建deepseek 请求体对象

```java
package com.hqyj.pojo;
import lombok.Data;
import java.util.List;
/**
 *  deepseek 请求体
 */
@Data
public class ChatRequest {
    //模型
    private String model;
    //聊天消息，可用于保留聊天记录
    private List<Message> messages;
    //流式返回
    private boolean stream = false;

    @Data
    public static class Message {
        //role：消息的角色，可以是 system（系统）、user（用户）、assistant（助手）或 tool（工具）
        private String role;
        // 消息的内容
        private String content;
    }
}
```

4  创建实现类代码，接口这里就不贴了，根据方法自行补上

```java
package com.hqyj.service.impl;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.hqyj.pojo.ChatRequest;
import com.hqyj.util.R;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import java.util.Collections;
import java.util.Map;

@Slf4j
@Service
public class ChatServiceImpl {

    @Value("${deepseek.api.url}")
    private String apiUrl;

    @Value("${deepseek.api.model}")
    private String model;

    public R chat(String question) {
        //创建deepSeek请求的聊天信息对象
        ChatRequest.Message message = new ChatRequest.Message();
        //设置角色
        message.setRole("user");
        //设置问题内容
        message.setContent(question);
        //创建deepSeek请求对象
        ChatRequest request = new ChatRequest();
        //设置聊天信息
        request.setMessages(Collections.singletonList(message));
        //设置模型
        request.setModel(model);
        //创建RestTemplate对象
        RestTemplate restTemplate = new RestTemplate();
        //发送POST请求
        Map<String,Object> map = restTemplate.postForObject(apiUrl, request, Map.class);
        System.out.println( map);
        return R.ok("success",map);
    }

}

```

5 控制接口

```java
 @RestController
@RequestMapping("/user-info")
public class UserInfoController {

@Autowired
    private ChatServiceImpl chatService;

@PostMapping("/chat")
    public R chat(@RequestBody String question)
    {
        return chatService.chat(question);
    }
}

```

6 测试接口 http://localhost:8081/user-info/chat