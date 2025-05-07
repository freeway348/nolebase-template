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
		return a > M.a; // 重载 < 运算符，使得该结构体使用sort时，将 a 从小到大排序，对 b 不操作
	}
}q[N];

sort(q, q + n);
```