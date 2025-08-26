# day02

---

1. 添加依赖：
```Java
<!--MySQL依赖-->  
<dependency>  
    <groupId>mysql</groupId>  
    <artifactId>mysql-connector-java</artifactId>  
    <!--版本号可不写，若不写，则自动拉取最新版本-->
    <version>5.1.49</version>  
</dependency>
```
2. yml配置MySQL连接：
```Java
server:  
  port: 9999  
  tomcat:  
    uri-encoding: utf-8  
spring:  
    datasource:  
      driver-class-name: com.mysql.jdbc.Driver  
      url: jdbc:mysql://localhost:3306/ai?useUnicode=true&characterEncoding=utf8&useSSL=false&allowPublicKeyRetrieval=true
      username: root  
      password:
```
3. 创建Mapper层UserMapper，继承BaseMapper\<User>，获得对entity层传来的User数据进行CRUD的功能
4. 创建service层的UserService，并在service层下创建实现层imp，并在imp下创建UserServiceImp.java文件
```Java
public class UserServiceImp extends ServiceImpl<UserMapper, User> implements UserService {   
}

// extends ServiceImpl<UserMapper, User> 是固定搭配
```
5. 完善entity层
6. Controller层做完注册功能后，使用Postman测试
	可在params中输入key和value来设置传输数据，随后通过POST请求将数据send到指定端口URL
	也可使用Body/row，在其中输入对应数据传输即可
		key是形参变量，value是实际传入值
```Java
Body/row如何设置key与value值：

{
"key1" : "value1",
"key2" : "value2"
}
```
```Java
//注册功能：UserController
@PostMapping("/register")  
public String Register(@RequestBody User user){  
  
    user.setFlag(0);  
    user.setCreatedate(java.time.LocalDateTime.now());  
    user.setUpdatedate(java.time.LocalDateTime.now());  
  
    userService.save(user);  
  
    return "success";  
}
```

### redis实现临时验证码发送

1. 导入依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```
2. 在edu.kust下新建util文件夹，用于存储编写生成四位验证码的函数的类，注意函数返回值应该为String类型，与RedisController的接收参数mail类型一致
3. 实现验证码设置和获取功能：
```Java
// RedisController
@RestController  
public class RedisController {  
  
    @Autowired  
    private RedisTemplate redisTemplate;  
  
    @GetMapping("/set")  
    public String save(String mail){  
  
        redisTemplate.opsForValue().set(mail, VerificationCode.Veri());  
        redisTemplate.expire(mail, 60, TimeUnit.SECONDS);  
        return "ok";  
    }  
  
    @GetMapping("/get")  
    public String get(String mail)  
    {  
        return (String) redisTemplate.opsForValue().get(mail);  
    }  
  
}
```
4. 打开QQ邮箱的SMTP，做完实训记得关，复制授权码后，配置properties，与yml配置文件在同一位置:
```properties
spring.mail.host=smtp.qq.com  
#自己的邮箱名  
spring.mail.username=xxxx@qq.com  
#邮箱的SMTP授权码  
spring.mail.password=xxxxxxx  
#smtp服务端口号  
spring.mail.port=465  
#配置 对smtp服务支持的java类  
spring.mail.properties.mail.smtp.socketFactory.class=javax.net.ssl.SSLSocketFactory  
#为了安全，需要配置SSL协议
spring.mail.default-encoding=UTF-8
```
5. 在MailController.java中完成该Post方法
```Java
@RestController  
public class MailController  
{  
  
    // 给别人发一封邮件  
    @Autowired  
    private JavaMailSender javaMailSender;  
  
    @PostMapping("/mail")  
    public String sendCode(String email){  
  
        // 一封邮件需要自己的邮箱、对方邮箱、主题以及内容  
        SimpleMailMessage message = new SimpleMailMessage();  
        message.setFrom("1656497721@qq.com");  
        message.setTo(email);  
        message.setSubject("验证码"); // 主题  
        message.setText("您的验证码是："+ VerificationCode.Veri() + "有效期 60 秒");  
        // 发送邮箱  
        javaMailSender.send(message);  
        return "success";  
    }  
  
  
}
```