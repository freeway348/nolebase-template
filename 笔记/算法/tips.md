、
# tips

1. 位运算： x+y=x^y+2(x & y)
	- x与y的和等于x异或y的值与2倍x与上y的值之和

### pair的sort

```C++
piar<int, int> g[N];

for (int i = 1; i <= n; i ++)
	cin >> g[i].first >> g[i].second;
	
sort(g, g + n); // 默认排序：以first为第一关键字，second为第二关键字，当first不同时，以first为主，将first从小到大排序，而first相同时，将会把second从小到大排序

若重写cmp函数如下：
bool cmp(pair<int, int> a, pair<int, int> b)
{
	return a.first < b.first; // 若不对second进行判断，则只会对first进行从小到大排序，而当first相同时也不会将second按从小到大排序
}
```

### 结构体的重写判断（用于sort）

```C++
struct Node
{
	int a, b;
	operator < (const Node &M)const
	{
		return  a < M.a; // 重载 < 运算符，使得该结构体使用sort时，将 a 从小到大排序，对 b 不操作
	}
}q[N];

sort(q, q + n);
```

### memset()的使用
#### 对整数型：

```C++
memset(dist, 0x3f, sizeof dist);
```
#### 对浮点数：

```C++
memset(dist, 0x7f, sizeof dist);
```

### stringstream的使用

```C++
使用：
int a[100];
int cnt = 0;
int p;
string line;
getline(cin, line) // 输入流，存储变量 (直接读取一行，也可用于消除换行)
stringstream ssin(line); // 将输入数据 line 转化为输入流
while(ssin >> p) // 相当于输入了一个数 p 
{
	a[cnt ++] = p; // 进行赋值
}
```
- 头文件：`#include <sstring>`


### 最大公约数GCD和最大公倍数LCM

```C++
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a; 
}

int lcm(int a, int b)
{
    return a/gcd(a,b)*b;
}

```