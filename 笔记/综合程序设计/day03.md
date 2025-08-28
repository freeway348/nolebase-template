# day03

---

实现人脸识别功能：
1. 事先准备：前往百度智能云开启人脸识别服务：[百度智能云人脸识别](https://console.bce.baidu.com/ai-engine/face/overview/index)
2. 创建完毕后，记住API与相关信息，后续进行功能调用会用到
3. 新建maven项目，用于人脸识别功能的实现
4. 先导入相关依赖，如果无法导入，则找到maven仓库直接导入依赖包：
```Java
<dependency>
    <groupId>com.baidu.aip</groupId>
    <artifactId>java-sdk</artifactId>
    <version>4.16.22</version>
</dependency>
```
5. 图片转换的Path方法选择java.nio.file包中的Path
```Java
// 准备图片资源，设置人脸库相关数据
String imageBase64 = "         "; // 由于图片转换为BASE64格式后内容过长，故初始化为空，后续将BASE64数据存入该变量，这样做更简便
String imageType = "BASE64";  // 默认选择使用BASE64格式存储人脸图像数据
String groupId = "default";  // 该人脸库的组别
String userId = "user1";  // 用户id


// 图片转换为BASE64格式存储
Path path = Paths.get("图片的绝对路径（一般放置于resources文件夹下）");  
byte[] imagesByte = Files.readAllBytes(path);  // 识别图片的数据类型
imageBase64 = Base64.getEncoder().encodeToString(imagesByte);
```
5. 如何实现人脸注册和人脸识别？---- 前往百度智能云人脸识别API-SDK文档中查看：[人脸识别API文档](https://ai.baidu.com/ai-doc/FACE/8k37c1rqz#%E4%BA%BA%E8%84%B8%E6%B3%A8%E5%86%8C)
```Java
// 根据API文档的相关设置，获取人脸注册和人脸识别（人脸搜索，在人脸库中进行搜索，并返回结果）

// 人脸注册
JSONObject res = aipFace.addUser(imageBase64, imageType, groupId, userId, null);
System.out.println(res.toString(2)); // 输出人脸注册结果

// 人脸识别(人脸搜索，在相应组别的人脸库中搜索)
JSONObject res = aipFace.search(imageBase64, imageType,groupId, null);  
System.out.println(res.toString(2));// 人脸识别结果

// 识别结果中的”score“即为该图片与人脸库中图片的相似度
```
6. 写人脸注册、人脸登录接口，使用Postman测试，一共完成五个接口，三种登录，两种注册


图片转换的Base64编码长度不同是因为图片清晰度不同，越清晰的图片转换后的Base64编码越长。

在html网页中显示图片：
	方法一：直接将图片拖拽到\<body> 中
	方法二：使用其Base64进行图片的传输：
```html
<body>
	 <img src="data:image/jpg;base64, "Base64编码">
</body>
```