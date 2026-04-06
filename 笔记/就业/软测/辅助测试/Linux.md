# Linux
### 一、简介

- 常见Linux发型版本：CentOS、Ubuntu
- Linux常见目录结构（所有文件都在根目录下统一管理）：
	- /：根目录
	- /root：超级管理员root的家目录
	- /home：普通用户家目录
	- /etc：配置文件存放目录
	- /bin：默认运行程序存放目录

- Linux命令构成：<font color="#ff0000">命令名 \[选项\] \[参数\]</font>
	- 命令名：表示命令的作用，必选
	- 选项：按照指定要求格式执行命令，中括号表示可选
	- 参数：命令操作的对象，中括号表示可选
- 例如：查看root目录下文件的详细信息
	- `ls -l /root`

- 注意事项：
	1. Linux命令需要<font color="#ff0000">严格区分大小写</font>
	2. Linux<font color="#ff0000">不支持鼠标</font>直接操作
	3. Linux命令前添加 <font color="#ff0000"># 表示注释</font>，添加注释后命令不执行

- Linux项目环境搭建准备：
	1. 服务器：本地服务器、云服务器
	2. 使用工具：finalshell远程连接
- 连接服务器四要素：
	1. 主机地址：服务器的IP（使用ifconfig命令可查看）
	2. 端口号：ssh端口号
	3. 用户名：Linux服务器账号
	4. 密码：Linux服务器密码

