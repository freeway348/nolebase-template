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
前置条件：注册一个账户

我们这里注册的账户为
```
手机号：16666666666
密码：123456
```
随后退出登录，再回到登录界面![](assets/Pasted%20image%2020260314204458.png)

- 自动化测试步骤：
	1. 输入用户名
	2. 输入密码
	3. 输入图形验证码
	4. 点击登录按钮
- 难点：使用`driver.find_element(定位方式, "值")`准确定位到元素位置
	- 定位方式：selenium的By工具下提供的8种元素定位方式（ID、CSS_SELECTOR、XPATH等）
		- ID：唯一
		- CSS_SELECTOR：速度快（推荐使用）
		- XPATH：根据元素标签所在页面的路径查找（不推荐）
- 解决方法（如何让AI使用CSS_SELECTOR写自动化测试代码）：
	1. AI写测试步骤的相关代码，然后由我们手动去写定位方式
	2. 将测试步骤提示词给AI的时候，把元素信息（具体使用哪种定位方式）给AI

#### 代码使用CSS_SELECTOR定位的值错误怎么办

1. 打开开发者工具（一般为F12，或者将鼠标放在Web前端界面指定位置“右键-检查”
	![](assets/Pasted%20image%2020260314205749.png)
2. 找到指定输入框的前端代码部分，并获取其Selector值（CSS选择器的值）
	![](assets/Pasted%20image%2020260314205954.png)
3. 这段代码复制获得到的CSS选择器的值为`#username`，将获取用户名输入框的语句`user_input = driver.find_element(By.CSS_SELECTOR, "值")`中的`"值"`替换为复制得来的该 CSS选择器 的值，其他输入框同理，即可正确识别定位到输入框
4. 修改代码后可成功进行自动化测试
	![](assets/Pasted%20image%2020260314225614.png)
	![](assets/Pasted%20image%2020260314225807.png)

```python
# 导入 selenium 所需的模块  
from selenium import webdriver  # 导入 WebDriver 管理器  
from selenium.webdriver.common.by import By  # 导入元素定位方式  
from selenium.webdriver.support.ui import WebDriverWait  # 导入显式等待工具  
from selenium.webdriver.support import expected_conditions as EC  # 导入预期条件  
from selenium.webdriver.firefox.service import Service  # 导入 Firefox 浏览器服务  
from selenium.webdriver.firefox.options import Options  # 导入 Firefox 配置选项  
import time  # 导入时间模块用于等待  
  
# 配置 Firefox 浏览器选项  
options = Options()  # 创建浏览器配置对象  
options.add_argument("--width=1920")  # 设置浏览器窗口宽度为 1920 像素  
options.add_argument("--height=1080")  # 设置浏览器窗口高度为 1080 像素  
  
# 启动 Firefox 浏览器  
firefox_service = Service()  # 创建 Firefox 浏览器服务对象  
driver = webdriver.Firefox(service=firefox_service, options=options)  # 启动 Firefox 浏览器实例  
  
try:  
    # 打开登录页面  
    driver.get("https://hmshop-test.itheima.net/Home/user/login.html")  # 访问黑马商城登录页面  
  
    # 创建显式等待对象  
    wait = WebDriverWait(driver, 10)  # 设置最大等待时间为 10 秒  
  
    # 步骤 1：输入用户名  
    username_input = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, "#username")))  # 等待用户名输入框出现  
    username_input.clear()  # 清空用户名输入框  
    username_input.send_keys("16666666666")  # 输入用户名 16666666666  
    # 步骤 2：输入密码  
    password_input = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, "#password")))  # 等待密码输入框出现  
    password_input.clear()  # 清空密码输入框  
    password_input.send_keys("123456")  # 输入密码 123456  
    # 步骤 3：输入图片验证码  
    verify_code_input = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, "#verify_code")))  # 等待验证码输入框出现  
    verify_code_input.clear()  # 清空验证码输入框  
    verify_code_input.send_keys("8888")  # 输入验证码 8888  
    # 步骤 4：点击登录按钮  
    login_button = wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR, ".J-login-submit")))  # 等待登录按钮可点击  
    login_button.click()  # 点击登录按钮  
  
    # 等待页面跳转  
    time.sleep(3)  # 等待 3 秒让页面完成跳转  
  
    # 验证登录结果  
    print(f"当前页面 URL: {driver.current_url}")  # 打印当前页面 URL    print(f"当前页面标题：{driver.title}")  # 打印当前页面标题  
  
    # 判断是否登录成功（根据实际页面情况调整判断条件）  
    if "login" not in driver.current_url.lower():  # 如果 URL 中不再包含 login        print("✅ 登录成功！")  # 打印登录成功消息  
    else:  
        print("⚠️ 登录可能失败，请检查验证码是否正确")  # 打印登录失败提示  
  
    # 保持浏览器打开观察结果  
    time.sleep(5)  # 等待 5 秒后关闭浏览器  
  
except Exception as e:  
    print(f"❌ 测试过程中出现错误：{str(e)}")  # 打印错误信息  
    time.sleep(5)  # 等待 5 秒便于观察  
  
finally:  
    # 关闭浏览器  
    driver.quit()  # 退出并关闭浏览器  
    print("浏览器已关闭")  # 打印关闭消息
```

### 四、断言
#### 1. 定义

断言：一种检查机制，用于验证某个条件，如果条件为真，则测试通过，如果条件为假，则测试失败

#### 2. 如何使用

函数方法：
```python
assert condition, message
```
- condition：要验证的布尔表达式，若为True，则断言通过；若为False，抛出异常
- message（可选）：当断言失败时显示的错误信息

##### 登录断言实现

- 获取文本的方式：driver.find_element(By.CSS_SELECTOR, "value").text
- 捕获断言：使用捕获断言可以实现当断言不通过时仍然执行后续代码段
```python
try:
	time.sleep(2)  # 需要等待2秒，因为代码执行速度太快，会出现页面未加载完毕就进行断言判断的情况，导致断言结果出错
	
except Exception as e:
	打印错误信息
```