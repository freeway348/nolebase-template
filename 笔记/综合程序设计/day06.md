# day 06

---

### 创建vue项目

1. 下载node.js，version18
2. 全局安装vue-cli脚手架：`npm install -g @vue/cli`，安装后，在命令行输入vue -V或vue --version查看vue-cli是否安装成功，若输出@Vue-cli 5.0.9，即安装成功
3. 新建文件夹用于存储vue项目，在命令行输入vue create \[项目名\] 
![](assets/Pasted%20image%2020250901091120.png)
![](assets/Pasted%20image%2020250901091132.png)
![](assets/Pasted%20image%2020250901091238.png)

4. 所有界面在main.js中进行挂载
5. 在/views文件夹中实现vue网页，所有的依赖都在node_modules中，我们只有一个index.vue一个入口，使用main.js进行绑定
6. 项目结构：![](assets/Pasted%20image%2020250901094827.png)
### 如何实现界面？

先在views下新建vue文件，在/router/index.js中设置路由，随后在App.vue中增添该路由


在<template\> 中实现div等网页组件的编写，在<style\>中实现CSS样式，在<script\> 中实现原<script\>方法，需要使用vue2写法
	注意，<script setup\> 是vue3写法，我们用不到
	一个template中只能有一个<div\>

实现按钮，v-on:click点击事件![](assets/Pasted%20image%2020250901140836.png)
![](assets/Pasted%20image%2020250901141234.png)

### 如何实现前后端连接通信

首先下载axios：npm install axios

![](assets/Pasted%20image%2020250901144330.png)![](assets/Pasted%20image%2020250901144333.png)![](assets/Pasted%20image%2020250901144346.png)
![](assets/Pasted%20image%2020250901144352.png)![](assets/Pasted%20image%2020250901144356.png)![](assets/Pasted%20image%2020250901144401.png)

1. E-R图
2. UML图
3. 架构图