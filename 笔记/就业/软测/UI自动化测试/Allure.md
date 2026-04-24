# Allure
### 一、Allure基本概念了解

Allure报告是一个开源的、多语言支持的自动化测试报告工具，旨在生成直观、交互式的`HTML测试报告`

### 二、Allure的安装与使用
#### 1. 安装

Allure报告的安装分为**两部分**：
1. allure-pytest插件
2. Allure命令行工具

#### 2. 使用

- 前置条件：当前环境已安装Java运行环境
1. 安装Pytest Allure插件：
	1. 安装：`pip install allure-pytest`
	2. 验证：`pytest --alluredir=report --version`
2. 安装Allure：
	- 方法1：使用 npm （推荐）：
		1. 确保 Node.js 和 npm 已安装
			- Node.js的安装和配置可以参考[该文章](https://zhuanlan.zhihu.com/p/2004975759790477711)
		2. 执行命令：`npm install -g allure-commandline --save-dev`
	- 方法2：手动安装
		1. 下载最新版本的allure-\*\.zip文件，[安装地址](https://github.com/allure-framework/allure2/releases)
		2. 解压到指定目录，如：/home/user/tools/allure-2.x.x
		3. 将 bin 子目录添加到系统 PATH 环境变量
		4. 验证安装：`allure --version`  输出类似 2.x.x，则表示安装成功
3. 使用allure生成测试报告
	1. 生成测试结果存储路径：`pytest --alluredir=report`
		- 表示将测试报告存储到当前目录的report文件夹下，若没有report文件夹，则自动创建一个
		- ![](assets/Pasted%20image%2020260315131646.png)
	2. 将结果转化为报告：`allure serve report`
		- 意思是将report下的结果文件都转化为报告
		- ![](assets/Pasted%20image%2020260315132521.png)
		- ![](assets/Pasted%20image%2020260315132539.png)
		- 需要注意的是，Allure使用的是本地网络地址，若不再使用allure，记得在后台CTRL+C关闭服务![](assets/Pasted%20image%2020260315132601.png)
