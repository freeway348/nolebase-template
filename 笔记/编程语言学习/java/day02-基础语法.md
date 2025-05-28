# day02-基础语法

---

### 一、方法

![](assets/Pasted%20image%2020250527164212.png)

![](assets/Pasted%20image%2020250527164335.png)
#### 方法的其他形式

需要判断如下情况：
	1. 方法是否需要接收数据处理
	2. 方法是否需要返回数据
		1. 无返回值：public static **void**
			无返回值的方法可以用 `return ;` 立即提前结束方法
#### 方法的重载

什么是重载？
	`一个类中`，出现多个方法的**名称相同**，但是它们的**形参列表不同**，那么这些方法就称为**方法重载**
方法重载不关心形参名称，如：
```
public static void getNum(int a, int b)
{
	......
}
与
public static void getNum(int x, int y)
{
	....
}

这两个是同一种方法，不能重复定义，不是方法重载
```

### 二、自动类型转换
#### 自动类型转换
1. 什么是自动类型转换？
	类型范围小的变量，可以直接赋值给类型范围大的变量，如：int 可以直接赋值给 long 
	![](assets/Pasted%20image%2020250527170353.png)
	自动转换的原理：将低位赋值后，类型范围大的变量会将高位自动全部补0
#### 强制类型转换

类型范围大的变量不能直接赋值给类型范围小的变量，会报错，所以需要用到强制类型转换

写法：类型  变量2 = （类型）变量1

![](assets/Pasted%20image%2020250527170947.png)
- 类型范围大的赋值给类型范围小的（强制类型转换后）可能会出现溢出

#### 表达式的自动类型提升（自动类型转换）

- 表达式的最终结果类型是由表达式中的**最高类型**决定的
- 在表达式中，byte、short、char 是直接转换成 int 进行运算的
	- 所以在方法中， byte + byte 得到的值是 int 类型的，所以需要 int 类型的返回值
### 三、输入与输出

输出：System.out.println();
输入：通过Java提供的Scanner程序来实现
	Scanner是Java提供好的API，程序员可以直接调用
	API（Application Programming Interface：应用程序编程接口），其实就是Java写好的程序，我们可以直接拿来调用；也是JRE中的核心类库中的东西

#### 如何输入？
步骤：
1. 导包：在package 和 public class 之间导入Scanner的包，这步不用主动做，写Scanner的时候IDEA会自动导入的
	![](assets/Pasted%20image%2020250528184736.png)
2. 创建对象：Scanner sc = new Scanner(System.in);
3. 输入：String username = sc.next(); 
	// 会让程序在这一行暂停，直到用户按下回车键，把名字交给变量username记住再往后走
### 四、运算符

1. Java中除法`/`：两个整数相除的结果还是整数
2. `+`号的特殊用法：
	1. 可作为连接符：`+`符号与**字符串**运算时是用作**连接符**的，其结果仍然是个字符串
```Java
int a = 5;
System.out.println("abc" + a); // 输出abc5
System.out.println(a + 5); // 输出10
System.out.println("itheima" + a + 'a'); // 输出itheima5a
System.out.println(a + 'a' + "itheima"); // 输出102itheima
```

3. `+=`、`-=`、`*=`、`/=`、`%=`这类扩展的赋值运算符隐含了强制类型转换，即：
```Java
a += b;  ------>  a = (a 的类型)(a + b);
```

#### 三元运算符

格式：条件表达式 ? 值1 : 值2
执行流程：首先计算条件表达式的值，如果为true，则返回值1，如果为false，则返回值2

#### 逻辑运算符

![](assets/Pasted%20image%2020250528190224.png)
![](assets/Pasted%20image%2020250528190429.png)

&& 和 & 的不同之处在于：&& 如果左边为 false，则右边不执行；|| 同理

