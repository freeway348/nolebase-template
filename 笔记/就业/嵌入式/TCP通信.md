# TCP通信
### 一、TCP通信流程

TCP是一个**面向连接的，安全的，流式传输协议**，这个协议是一个`传输层协议`。
- 这些都是TCP连接自带的，只要使用TCP连接，就会具有这些特性
- 面向连接：是一个**双向连接**，通过**三次握手**完成，断开连接需要通过**四次挥手**完成。
- 安全：tcp通信过程中，会对发送的每一数据包都会进行**校验**, 如果发现数据丢失, 会**自动重传**
- 流式传输：发送端和接收端处理数据的速度，数据的量都可以不一致
	- 只要连接双方未断连，双方之间就存在一个"**管道**"，数据就在"**管道**"中流动，如果管道中的数据满了，发送端就会等待，否则发送端就会一直发送。而接收端的接收速度、每次接收的数据量都可以不一样

TCP通信流程：
![](assets/Pasted%20image%2020260317203512.png)

使用套接字通信时，必须先启动服务器端的程序，再启动客户端的程序，否则会出错

#### 1. 服务器端通信流程

1. 创建用于监听的套接字, **这个套接字是一个文件描述符**：
```c
int lfd = socket();
```
2. 将得到的监听的文件描述符和本地的IP 端口进行绑定：
```c
bind();
```
3. 设置监听(成功之后开始监听, 监听的是客户端的连接)：
```c
listen();
```
4. 等待并接受客户端的连接请求, 建立新的连接, 会得到一个新的文件描述符(通信的)，没有新连接请求就阻塞：
```c
int cfd = accept();
```
5. 通信，读写操作默认都是阻塞的：
```c
// 接收数据
read(); / recv();
// 发送数据
write(); / send();
```
6. 断开连接, 关闭套接字：
```c
close(); // 客户端执行close()函数，就会调用两次挥手，若服务器端也调用close()函数，则又会进行两次挥手，一共四次挥手
```
#### 2. 客户端的通信流程

> 在单线程的情况下客户端通信的文件描述符有一个, 没有监听的文件描述符

1. 创建一个通信的套接字
```c
int cfd = socket();
```
2. 连接服务器, 需要知道服务器绑定的IP和端口
```c
connect();
```
3. 通信
```c
// 接收数据
read(); / recv();
// 发送数据
write(); / send();
```
4. 断开连接, 关闭文件描述符(套接字)
```c
close();
```


### 二、sockaddr 数据结构

![](assets/Pasted%20image%2020260317223202.png)
```c
// 在写数据的时候不好用
struct sockaddr {
	sa_family_t sa_family;       // 地址族协议, ipv4
	char        sa_data[14];     // 端口(2字节) + IP地址(4字节) + 填充(8字节)
}

typedef unsigned short  uint16_t;
typedef unsigned int    uint32_t;
typedef uint16_t in_port_t;
typedef uint32_t in_addr_t;
typedef unsigned short int sa_family_t;
#define __SOCKADDR_COMMON_SIZE (sizeof (unsigned short int))

struct in_addr
{
    in_addr_t s_addr;
};  

// sizeof(struct sockaddr) == sizeof(struct sockaddr_in)
struct sockaddr_in
{
    sa_family_t sin_family;		/* 地址族协议: AF_INET */
    in_port_t sin_port;         /* 端口, 2字节-> 大端  */
    struct in_addr sin_addr;    /* IP地址, 4字节 -> 大端  */ // 根据typedef可知，in_addr结构体内的元素是in_addr_t类型，也即unsigned int类型
    /* 填充 8字节，用不到*/
    unsigned char sin_zero[sizeof (struct sockaddr) - sizeof(sin_family) -
               sizeof (in_port_t) - sizeof (struct in_addr)];
};  
```