# day04-数组

---

### 静态数组
```Java
//数组定义方式：
String[] names = {"张三", "李四", "王五"};
String[] names = new String[]{"张三", "李四", "王五"};
String names[] = new String[]{"张三", "李四", "王五"};

//数组下标索引默认从0开始
// names.length：数组元素个数

System.out.println(names); // 数组名输出的是地址值
```

### 动态数组

动态：定义数组时先不存入具体元素值，只确定数组存储的数据类型和数组长度
![](assets/Pasted%20image%2020250615144808.png)
```Java
double[] scores = new double[5]; // 定义5个数组元素，double默认值为0.0
double max = scores[0];
for (int i = 0; i < scores.length; i ++)
{
	double data = scores[i]; // 这样能少访问几次数组，优化性能，减少访问时间：优化了if和if判断通过后的数组访问
	if (max < data)
		max = data;
}
System.out.println("最大值为："+max);
```

### 二维数组

```Java
二维数组：
// 静态：
String[][] names = {
	{"赵三", "器单"},
	{"李四", "王五"},
	{"七普", "六六"}
}

//动态：
int[][] arr = new int[3][5];
// 数组名称[行索引][列索引]，索引都是从0开始的

System.out.println(arr.length); // 会输出行索引的个数
System.out.println(arr[1].length); // 会输出arr[1]的列索引元素个数
```
