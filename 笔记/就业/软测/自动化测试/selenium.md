# selenium
### 一、 selenium可以用来做什么？

selenium是一个用于**web**应用程序**测试**的工具，可以使用代码对web页面进行操作，完成web应用程序自动化测试

### 二、使用selenium的步骤

编写代码执行以下步骤以实现web自动化测试：
1. 启动浏览器
2. 打开百度首页
3. 进行搜索操作
	1. 查找搜索文本框，输入搜索关键字
	2. 查找“百度一下”按钮，点击按钮，开始搜索
4. 关闭浏览器
```python
# 导包
from selenium import webdriver
# 1. 启动浏览器，通过浏览器操作界面
driver = webdriver.Chrome()  # 获取谷歌浏览器对象；如果是Edge浏览器可以使用webdriver.Edge()
# 2. 打开百度首页
driver.get("https://www.baidu.com")
# 3. 搜索
搜索框元素 = driver.find_element(定位方式, 值) # 查找元素方法，返回元素
搜索框元素.send_keys("内容") # 给元素输入内容方法
按钮元素.click() # 点击元素方法
# 4. 关闭浏览器
driver.quit()
```

### 三、项目实战

项目测试地址：[Tpshop开源商城](https://hmshop-test.itheima.net/Home/user/login.html)
