# day01

---

1. 新建项目
	![](assets/Pasted%20image%2020250825090849.png)
2. 新建resources文件，将其标记：![](assets/Pasted%20image%2020250825091635.png)
3. 在resources/application.yml文件中编写端口号，用于在前端该端口进行访问，并配置MySQL连接：
```Java
server:  
  port: 9999  
  tomcat:  
    uri-encoding: utf-8  
spring:  
    datasource:  
      driver-class-name: com.mysql.jdbc.Driver  
      url: jdbc:mysql://localhost:3306/user_db?useUnicode=true&characterEncoding=utf8&useSSL=false&allowPublicKeyRetrieval=true  
      username: root  
      password: xxxxxxxx
```


完成项目的初步搭建后，进行需求分析，随后完成原型图的绘制（原型设计）