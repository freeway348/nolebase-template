# Day02

---

### 一、昨日回顾

#### mybatis-plus设置

对jdbc及连接池设置完成后，继续在resources/application.properties文件中完成对mybatis-plus的配置
```
# mybatis-plus  
# classpath默认会找到resources  
# classpath:/mapper/UserMapper.xml只能加载一个xml文件  
# * 是通配符，表示0到所有，可以匹配所有xml文件  
# 扫描xml文件  
mybatis-plus.mapper-locations=classpath:/mapper/*.xml  
# 关闭缓存（查询时可能会用到，可能会有更新延迟）（对数据实时性要求较高时关闭此项）  
mybatis-plus.configuration.cache-enabled=false  
# 数据字段中如果有下划线，则将带有下划线的字段转化为小驼峰表示法  
# 小驼峰：字段由多个单词组成，第一个单词首字母小写，其他单词首字母大写（如：userLogin），一般用于变量定义  
# 大驼峰：字段由多个单词组成，每个单词首字母大写（如：UserLogin、DruidDataSource），一般用于类名、接口、枚举等定义  
mybatis-plus.configuration.map-underscore-to-camel-case=true
```

### 二、Day02开讲
#### 为什么文件都要放在com.springboot.userserver包下

因为@ComponentScan默认扫描的就是com.springboot.userserver下的文件，如果放在外边，则无法识别到代码

#### UseController

![](assets/Pasted%20image%2020250624091557.png)
该文件夹下存储的是前端控制器，其中，controller，service注释：标记注释，spring在启动前通过自动装配扫描标记注解，将带有该类注解的类加载到IOC容器中

前端控制器发送请求需要接口定义：
```Java
/*
* 以登录功能为例：
*  url：由域名+接口路径组成；  域名：http://localhost:8088，接口路径：/user/login  
*  url:"http://localhost:8088/user/login"  
*  method(请求类型)： POST          GET请求一般做查询，POST请求一般在数据提交时使用 
*  params：{     请求参数，可选  
*      "username":"admin",  
*      "password":"123456"  
*  }  
*  response：{  
*      "code":200,          表示一个状态码： 200 --- 成功； 500 ------- 失败  
*      "msg":"登录成功"        msg用于信息提示： 登录成功； 用户名/密码错误  
*      "data":{}              可选，如果需要返回数据，则将数据存在该字段中  
*  }
*/
```
```Java
// RequestMapping： 定义url路径的注解，分为两种，分别是类和方法的定义；    类------url路径的公共部分，方法------具体某个接口的路径，完整url路径=类路径+方法路径
```


#### UseMapper

![](assets/Pasted%20image%2020250624091709.png)

@Mapper类注解用于将该mapper层的类（带注解的）加载到IOC容器中

#### UseServerApplication

![](assets/Pasted%20image%2020250624093204.png)

该文件中存储的是启动类，使用MapperScan类注解去扫描指定包路径下的所有Mapper接口，并加载到IOC容器中
```Java
@MapperScan(basePackages = "com.springboot.userserver.*.mapper")
```


#### 原理解析图示讲解

![](assets/Pasted%20image%2020250624094755.png)

### 后端具体代码实现及解释

```
主要步骤可参照上述所给的原理解析图示讲解的详细图示示例
```
#### 完成前端控制器/user/controller/UserController的内容实现

```Java
@RestController
@RequestMapping("/user")
public class UserController {


    // @RequestMapping(value = "/login", method = RequestMethod.POST) // 两个RequestMapping组成类和方法的url路径之和
    // 写法太麻烦了，以下用另一种写法来实现相同功能
    @PostMapping("/login")
    public JSONObject login(@RequestBody LoginUser loginUser){ // @RequestBody用在POST请求上，只解析json格式的请求参数，将json数据转换成LoginUser对象
        // 1. 将请求参数LoginUser转发给service层处理
        

        // 2. 获取处理结果，返回给前端
        
    }
}
```
JSONObject表示返回值是JSON格式的数据
![](assets/Pasted%20image%2020250624110947.png)
	UserController会将从LoginUser中得到的请求发送给service层处理
在/user包下再创建一个Package，命名为dto，并在dto 包下创建LoginUser，用于存储UserController的login方法提供参数，以下是对LoginUser.java文件的编辑 ：
![](assets/Pasted%20image%2020250624103818.png)

#### service层的方法声明及代码实现

1. 在service包下的UserService中对方法进行声明![](assets/Pasted%20image%2020250624110558.png)
	接口类中默认所有方法都是public，所以不用再写public修饰
2. 在接口类中声明后，需要在/service/impl/UserServiceImpl中对该方法进行具体功能实现：
	![](assets/Pasted%20image%2020250624111440.png)
	![](assets/Pasted%20image%2020250624111515.png)![](assets/Pasted%20image%2020250624111546.png)
3. 完成对loginUser方法的具体实现，用于处理接收到的登录请求数据，最终用来判断用户名与密码是否正确，返回判断结果
```Java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {

    @Override
    public boolean loginUser(LoginUser loginUser) {
        // 1. 把username和password构造成sql的筛选条件
        // sql：select * from user where username = ? and password = ?
		
		// 该User是/entity/User类
        // 单表查询使用mybatis-plus提供的条件构造器，该构造器只适用于单表查询
        QueryWrapper<User> w = new QueryWrapper<>();
        // 第一个参数填写的不是User对象的属性名，而是数据库数据表中的字段名
        w.eq("username", loginUser.getUsername());
        // eq相当于sql:username = ?
        w.eq("password", loginUser.getPassword());
        // eq相当于sql：password = ?
        // 如果构造器连续调用方法，则方法之间默认以and连接，构成的完整sql语句就是：username = ? and password = ?
        // 如果要做or关系，则需要显式调用or方法


        // 2. 把sql语句提交给mysql，获取结果
        User user = this.getOne(w);

        // 3. 将结果返回给controller
        if (!ObjectUtils.isEmpty(user)) // 如果非空
            return true;

        return false;
    }
}
```
4. 获取到返回消息后，再将处理结果同样用JSON格式返回给前端输出结果
```Java
@RestController
@RequestMapping("/user")
public class UserController {

    @Resource // 做依赖注入，从SpringbootIOC容器中取对象
    private UserService userService; // 取的对象及对应的变量即为该行

    // @RequestMapping(value = "/login", method = RequestMethod.POST) // 两个RequestMapping组成类和方法的url路径之和
    // 写法太麻烦了，以下用另一种写法来实现相同功能
    @PostMapping("/login")
    public JSONObject login(@RequestBody LoginUser loginUser){ // @RequestBody用在POST请求上，只解析json格式的请求参数，将json数据转换成LoginUser对象
        // 1. 将请求参数LoginUser转发给service层处理
        boolean result = userService.loginUser(loginUser); // 返回是否登陆成功的结果

        // 2. 获取处理结果，返回给前端
        JSONObject obj = new JSONObject();
        
        if (result){
            obj.put("code", 200);
            obj.put("msg", "登陆成功");
        }else{
            obj.put("code", 500);
            obj.put("msg", "用户名或密码错误");
        }

        return obj;
    }
}

```
5. 到目前为止，登录功能后端代码基本实现
### 前端实现

1. 创建普通项目，选择模板为基本html模板![](assets/Pasted%20image%2020250624113314.png)
2. 