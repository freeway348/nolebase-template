# day05-面向对象编程

---

### 什么是对象？

对象是一种特殊的数据结构，可以用来记住一个事物的数据，从而代表该事物
例：
```Java
public static void main(String[] args){
	// 创建赵丽颖的Star对象并赋值
	Star s1 = new Star();
	s1.name = "赵丽颖";
	s1.age = 36;
	s1.height = 165.0;
	s1.weight = 44.6;
}
```

### 如何设计对象？

1. 先设计对象的模板，也就是对象的设计图：**类**，例如：
```Java
public class Star{
	String name;
	int age;
	double height;
	double weight; // 这些都是Star对象的属性
}
```
2. 通过new关键字，每new一次类就得到一个新的对象，例如：`Star s1 = new Star()`，就获得了一个对象s1
```Java
public class Test {
    public static void main(String[] args) {
        // 目标：学会创建对象
        // 格式：类型 对象名 = new 类名();
        Star s1 = new Star(); // s1是个地址，指向new Star()这个明星对象
        s1.name = "王宝强";
        s1.age = 43;
        s1.height = 165;
        s1.weight = 54;
        s1.gender = "男";
        System.out.println(s1.name);
        System.out.println(s1.age);
        System.out.println(s1.height);
        System.out.println(s1.weight);
        System.out.println(s1.gender);
    }
}
```

### 封装的思想

```Java
// 封装思想，把数据和对数据的处理放到同一个类中，可以减少重复代码的书写
public class Student {
    String name;
    double chinese;
    double math;
    
    public void printAllScore(){
        System.out.println(name + "的总成绩是： " + (chinese + math));
    }
    public void printAvgScore(){
        System.out.println(name + "的平均成绩是： " + (chinese + math) / 2);
    }
}
```
这样一来，只要在建立该类的对象时，若要求总成绩，则直接调用其方法即可
```Java
public class Test2 {
    public static void main(String[] args) {
        // 目标：创建学生对象存储学生数据，并操作学生数据
        Student s1 = new Student();
        s1.name = "波妞";
        s1.chinese = 100;
        s1.math = 100;
        s1.printAllScore(); // 直接调用，更加便利，减少对重复代码的编写
        s1.printAvgScore();

        Student s2 = new Student();
        s2.name = "播仔";
        s2.chinese = 50;
        s2.math = 100;
        s2.printAllScore();
        s2.printAvgScore();
	    // 不同对象调用方法时，该方法会自动去找对应对象中存储的数据，不会混淆
    }
}
```

### 对象到底是啥？

对象本质上是一种**特殊的数据结构**（可以理解为**一张表**）
例：![](assets/Pasted%20image%2020250618164500.png)
class就是**类**，也称为**对象的设计图**（或者**对象的模板**），每new一个新对象，就相当于为其建了张新表
### 对象在计算机中是啥？

![](assets/Pasted%20image%2020250618165844.png)
首先，创建完学生类和main类后，将会在方法区创建Test.class和Student.class的空间，并包含有其变量方法等内容；
随后，在main中创建类，会先将main压入栈内存并分配一部分空间，还会在堆内存处分配一块内存空间（有其自己的对象和属性），然后会在栈内存中的main空间处创建Student s1对象，并根据从堆内存中获取的地址赋给s1，使得对对象s1进行操作修改时，就会找到其对应地址处，并将数据赋值给对应的变量，若调用方法，则会到对应对象中，然后找到该对象的类的地址，再从该类的地址处找到方法，然后将对应方法调入栈内存，用完后清除出栈，空出空间。

### 什么是面向对象编程？

万物皆对象，谁的数据谁存储

### 构造器

构造器是类的一种成分，构造器组成如下：
```Java
public class Student{
	// 构造器
	// 构造器是一种特殊方法，没有返回值类型声明，名字必须与类名一致
	public Student(){
		...
	}
}
```

特点：**创建对象**时，对象会自动调用对应的匹配构造器，例：
```Java
public class Student {
	String name;
	int age;
    // 1. 无参数构造器
    public Student() {

    }
    // 2. 有参数构造器
    public Student(String n, int a) {
		name = n;
		age = a;
    }
    // 构造器重载
    public Student(String n) {
        name = n;
    }
}

```
```Java
public class Test {
    public static void main(String[] args) {
        // 目标：搞清楚类的构造器，搞清楚其特点和常见应用场景
        Student s1 = new Student(); // 创建该对象时，会自动调用Student()构造器
        Student s2 = new Student("张三");// 创建该对象时，会自动调用Student(String n)构造器
    }
}
```

构造器的常见应用场景：创建对象时，同时完成对对象成员变量（属性）的**初始化赋值**

>[!tip] 注意事项
>1. 类默认就自带了一个无参构造器
>2. 如果为类定义了有参数构造器，那么类默认自带的无参构造器就没有了，此时如果还想用无参数构造器，就必须自己手写一个无参构造器出来
>3. 总结：要么都不写，要么都写（对于无参构造器和有参数构造器）

### this关键字

```
对象中的变量是成员变量，方法中的变量是局部变量
```

this就是一个**变量**，可以用在方法中，来`拿到当前对象`（拿到的是该对象的地址，所以此时可以把this关键字当做该对象来使用）

作用：this主要是用于`解决变量名称冲突问题`的
```Java
//例如：
public class Student()
{
	String name;
	public void printHobby(String name)
	{
		System.out.println(this.name + "喜欢" + name); // this获取的是该对象，则this.name代表的就是对象的name，而name代表的才是传入的参数
	}
}
```
### 封装

>[!tip] 面向对象的三大特征
>封装、继承、多态

例如：类就是一种封装 ---> 就是用类设计对象处理某一事物的数据时，应该把要处理的数据，以及处理这些数据的方法，设计到一个对象中去

#### 封装的设计要求

1. 合理隐藏 ----- 隐藏成员，使用private（私有，隐藏）进行修饰
2. 合理暴露 ----- 公开成员，可以使用public（公开）进行修饰
将类中的成员变量私有化（public），使用getter和setter（公开/public）来对对象的成员变量进行修改和访问

### 实体类
#### 什么是实体类？

```
具体代码可见day05- package：javabean
```
实体类是一种特殊类，类中要满足如下需求：
1. 要求1：类中成员变量全部私有化，并提供**public修饰**的**getter**和**setter**方法
2. 要求2：类中需要提供一个无参数构造器，有参数构造器可写可不写（若写有参数，则需同时写无参数）
实体类的基本作用：创建它的对象，存储数据（封装数据）

示例：
```Java
package com.itheima.javabean;

public class Student {
    // 私有成员变量
    private String name;
    private double chinese;
    private double math;

    // 提供无参构造器
    public Student() {
    }


    // 提供有参数构造器
    public Student(String name, double chinese, double math) {
        this.name = name;
        this.chinese = chinese;
        this.math = math;
    }

    // 提供公开的getter和setter方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getChinese() {
        return chinese;
    }

    public void setChinese(double chinese) {
        this.chinese = chinese;
    }

    public double getMath() {
        return math;
    }

    public void setMath(double math) {
        this.math = math;
    }
}
```
```Java
package com.itheima.javabean;

public class Test {
    public static void main(String[] args) {
        // 实体类的基本作用：创建它的对象，存储数据（封装数据）
        Student s1 = new Student();
        s1.setName("小王");
        s1.setChinese(100);
        s1.setMath(100);
        System.out.println(s1.getName());
        System.out.println(s1.getChinese());
        System.out.println(s1.getMath());

        Student s2 = new Student("小六", 80, 90);
        System.out.println(s2.getName());
        System.out.println(s2.getChinese());
        System.out.println(s2.getMath());

    }
}
```

#### 实体类的应用场景

分层思想：实体类的对象`只负责`数据存取，而对数据的**业务处理交给其他类的对象来完成**，以实现数据和数据业务处理相分离

### static静态变量

- static可用来修饰成员变量、成员方法
- 成员变量根据有无static修饰，分为两种：
	1. 静态变量（类变量）：有static修饰，属于类，在计算机中只有一份，**会被类的所有对象共享**
	2. 实例变量（对象的变量，实例就是对象）：无static修饰，是**属于每个对象**的，每个对象都有一份，每个对象访问时会访问它自己所拥有的实例变量
![](assets/Pasted%20image%2020250622161713.png)
如该图所示，在Student学生类中，有static修饰的静态变量`name`和无static修饰的实例变量`age`，当创建s1、s2对象时，会为每一个对象新建一个age，该age实例变量属于对应的对象，而name是属于类的，被对象s1和s2共享使用

#### 如何访问静态变量？

1. 类名.静态变量（**推荐**）
2. 对象.静态变量（可用，但**不推荐**）
而访问实例对象则只能用如下写法：对象.实例对象
```Java
// Student 类

public class Student {
    // 静态变量：有static修饰，属于类，只加载一份，可以被类的全部对象共享访问
    static String name;
    // 实例变量：无static修饰，属于对象，每个对象都有一份
    int age;
}
```
```Java
// Test类，用法示例
public class Test {
    public static void main(String[] args) {
        // 目标：认识static修饰成员变量，特点、访问机制，搞清楚作用
        // 1. 类名.静态变量（推荐）
        Student.name = "袁华";
        System.out.println(Student.name);

        // 2. 对象名.静态变量（不推荐）
        Student s1 = new Student();
        s1.name = "张三";

        Student s2 = new Student();
        s2.name = "王五";

        System.out.println(s1.name);
        System.out.println(Student.name);

        // 3. 对象名.实例变量
        s1.age = 23;
        s2.age = 18;
        System.out.println(s1.age);
        System.out.println(s2.age);
    }
}
```

#### 具体实现原理

```
这是针对上述静态变量示例代码所述，不影响了解原理
```
![](assets/Pasted%20image%2020250622164234.png)
**初始分配内存区域**：系统内有栈内存、堆内存和方法区的存在，首先会先加载Test类（包括Test类下的所有内容（包括main方法））进入方法区，再将main方法提取到栈内存中执行，随后在main方法执行过程中遇到了Student类，随后Student类会加载到方法区（此时会同时扫描Student类中是否有static修饰的变量，如果有，则立即加载一份到堆内存中，为其开辟一块内存区域，并以默认值存储）
**使用类名访问静态变量**：接下来可以直接通过类名去访问静态变量（通过类名找到该静态变量的地址，并由此在堆内存找到并对其赋值）
**使用对象名访问静态变量**：首先在main方法中建立了对象后，会在栈内存的main区域中为其分配部分内存空间，并在堆内存中开辟一块内存空间供其存储数据（类似上图的s1对象表），栈内存中的对象会有一个地址指向其在堆内存中的对应数据表地址，而堆内存中的数据表中也存有Student类的地址，故可找到Student类。当访问静态变量时，对象通过栈内存中的地址找到其在堆内存中的数据表，再根据该表所指向的Student类地址，找到Student类，随后在Student类中找到静态变量name的地址，再根据该地址到堆内存中对其进行修改

使用类名访问实例变量时会报错，因为类不知道要访问的是哪个对象的实例变量

#### 静态变量的应用场景

- 如果某个数据只需要一份，且希望能够被共享修改、访问，则该数据可以定义成静态变量来记住
- 案例说明：系统启动后，要求用户类可以记住自己创建了多少个用户对象
```Java
public class User{
	// 静态变量
	public static int number; // 不赋值，则为默认值0

	// 构造器
	public User(){
		User.number ++; // 每次new创建一个新对象都会使number+1
		// 也可写为 number++，因为访问自己类中的类变量，可以省略类名不写；如果是在某个类中访问其他类里的变量，则必须带类名访问
	}
}
```

### static修饰方法

```
基本与static修饰变量相同
```
#### 成员方法的分类

1. 静态方法：有static修饰的成员方法，属于类
	- 访问方法：
		1. 类名.静态方法（**推荐**）
		2. 对象名.静态方法（**不推荐**）
2. 实例方法：无static修饰的成员方法，属于对象
	- 访问方法：对象.实例方法

#### 设计规范

如果方法只是为了做一个功能且**不需要**直接访问**对象的数据**，这个方法直接定义为静态方法；如果这个方法是对象的行为，需要访问对象的数据，则该方法必须定义成实例方法

#### 为什么main方法是静态方法？

因为main方法是程序的入口，官方是直接用类名.main直接调用的

#### 静态方法的常见应用场景

- 做工具类
	- 工具类中的方法都是一些静态方法，每个方法用来完成一个功能，以便供给开发人员直接使用

使用类方法来设计工具类的好处：提高了代码的复用，调用方便，提高了开发效率

即：将静态方法都放到一个工具类中，该工具类不做其他事情，仅用于调用静态方法来减少代码重复书写

#### 为什么工具类中的方法要用静态方法，而不用实例方法？

因为实例方法需要创建对象来调用，此时对象只是为了调用方法，而对象会占用内存，这样会浪费内存；而使用静态方法，只要直接类名调用即可，调用方便，也能节省内存（不用新创建对象了）

>[!tip] 重要提点
>工具类没有创建对象的必要，建议将工具类的构造器私有化
```Java
// 假设以下是工具类的内容：
public class VerifyCodeUtil{
	// 私有化构造器，因为无需使用构造器创建对象
	private VerifyCodeUtil{
		
	}
	// 工具类的静态方法代码
	// 生成随机验证码
	public static String getCode(int length)
    {
        String code = "";
        for (int i = 0; i < length; i++)
        {
            int number = (int)(Math.random() * 3);
            switch (number){
                case 0 :
                    int number1 = (int)(Math.random() * 10);
                    code += number1;
                    break;
                case 1 :
                    char ch = (char)(Math.random() * 26 + 'a');
                    code += ch;
                    break;
                case 2 :
                    char ch1 = (char)(Math.random() * 26 + 'A');
                    code += ch1;
                    break;
            }
        }
        return code;
    }

}
```
