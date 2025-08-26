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