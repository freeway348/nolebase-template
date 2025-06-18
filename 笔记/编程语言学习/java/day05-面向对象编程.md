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
