# postman断言
### 一、postman断言

```
注意！！！Tests标签是用于后置操作脚本的编写，不仅仅是用于断言
```
- 介绍：借助JavaScript语言编写代码，自动判断预期结果与实际结果是否一致
- 断言代码在Tests的标签中![](assets/Pasted%20image%2020260422204331.png)
#### 1. 常用断言
##### 1）🌟断言响应状态码

```
可以根据状态码判断请求是否被服务器正确处理，若请求码为200，则说明请求成功
```
- 点击右侧的`Status code:Code is 200`就可以自动生成JS断言代码![](assets/Pasted%20image%2020260422204956.png)
```JavaScript
// 断言响应状态码是否为200
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200); // 200 是预期结果
});
/*
pm：postman的一个实例
test()：pm实例的一个方法。有两个参数
	参数1：在断言结束后给出的文字提示，可以修改。
	参数2：匿名函数（function及后续实现都是匿名函数的一部分）
pm.response.to.have.status(200);  -->  意思是：postman的响应结果中应该含有状态码 200
*/
```
- 设计用例完成后，对响应码进行断言。发送请求，获得响应体后会自动执行断言代码，可在`Test Results`中查看断言结果![](assets/Pasted%20image%2020260422205633.png)
##### 2）断言响应体中是否包含某个字符串

- 右侧代码点击：`Response body:Contains string`
- 断言结果查看：![](assets/Pasted%20image%2020260422211814.png)
	- 选择post方法，向指定URL的指定接口提交请求
	- Headers请求头为：`Content-Type: application/json` -->  描述的是**客户端发送给服务器的请求体（Request Body）的格式**。而**客户端通过 `Accept` 请求头告知服务器它能理解的格式，服务器会“尽可能”根据 `Accept` 头返回匹配的格式，但无法保证 100% 满足**。如果请求中不带 `Accept` 头，服务器会根据自己的默认配置（如默认返回 JSON）或路由逻辑来决定响应格式。同时，服务器返回的响应头中也使用`Content-Type`来告知客户端自己返回的响应体格式。
	- Body中是需要传递的接口测试用例
	- Tests中是断言语句
```JavaScript
// 断言响应体中是否包含指定字符串
pm.test("Body matches string", function () {
    pm.expect(pm.response.text()).to.include("string_you_want_to_search");
});
/*
pm：postman的一个实例
test()：pm实例的一个方法。有两个参数
	参数1：在断言结束后给出的文字提示，可以修改。
	参数2：匿名函数
pm.expect(pm.response.text()).to.include("string_you_want_to_search");  
	-->  意思是：postman期望响应文本中包含"string_you_want_to_search"（include()方法中的参数可修改，是预期结果）
*/
```
##### 3）断言响应体是否等于某个字符串（预期是一个对象）（是对整个响应体进行判断）

> 若返回的响应体中存在变化的值，则该方法不可用（因为永远不可能成功）
- 右侧代码点击：`Response body:is equal to a string`
```JavaScript
// 断言 响应体 是否等于某个字符串（对象）
pm.test("Body is correct", function () {
    pm.response.to.have.body("response_body_string");  // --> 预期结果
});
/*
pm：postman的一个实例
test()：pm实例的一个方法。有两个参数
	参数1：在断言结束后给出的文字提示，可以修改。
	参数2：匿名函数
pm.response.to.have.body("response_body_string");
	-->  意思是：postman的 返回响应体 中应该有 响应体"response_body_string"（可以先测试一次，再将测试返回的整个正确响应体复制到body()中）
*/
```
##### 4）🌟断言JSON数据

- 右侧单击：`Response body:JSON value check`
```JavaScript
pm.test("Your test name", function () {
    var jsonData = pm.response.json();  // 代表响应的json结果存入jsonData中
    pm.expect(jsonData.value).to.eql(100); // 响应体中，jsonDate.value 应该等于 100 （其中，value应该为响应体的 key）
});
// var jsonData：用js语法定义一个变量 ，jsonData就是变量名
// jsonData.value：代表jsonData的 键为"value"的 value值
// 以下图为例：pm.expect(jsonData.code).to.eql(10000)
```
- pm.response.json()获取的就是返回的整个响应体（响应体就是json格式的，这是Restful API的默认返回格式）![](assets/Pasted%20image%2020260422213308.png)
- 实际场景测试断言：![](assets/Pasted%20image%2020260422214711.png)
##### 5）断言响应头

- 右侧点击：`Response headers:Content-Type header check`
```JavaScript
pm.test("Content-Type is present", function () {
    pm.response.to.have.header("Content-Type");
});
// pm.response.to.have.header("Content-Type"); -->  postman的响应内容应该包含响应头"Content-Type"，但用处不大，因为大部分响应头都有Content-Type。
/* 
为此，我们需要对该内容进行补足：pm.response.to.have.header("Content-Type", "application/json");
--> 即：Content-Type(key)的值应该为application/json(value)，但不能有多余value值（这不是include判断，这是equal判断）
*/
```
#### 2. 工作原理

1. 在Postman界面中组织http请求方法、url、请求头、请求体，并在Tests中添加断言代码
2. 点击send按钮后，进入Postman内部（不可见）
3. 在Postman内部完成了对http请求方法、url、请求头请求体等的分析和组织，随后将http请求发送给服务器
4. 服务器解析http请求，随后回发http响应，响应进入postman内部，postman自动执行断言代码，得到断言结果，再将断言结果返回Postman界面的Test Results中显示
- 图示说明：![](assets/Pasted%20image%2020260422220426.png)
