# ST表

---

用于处理求区间最大/最小值查询问题（RMQ问题），主要使用倍增思想，可以实现$O(n\log n)$ 预处理，O(1) 查询，静态维护
![](assets/Pasted%20image%2020250516153540.png)
使用位运算处理幂级次

```C++
预处理：
// f[i][j]: i 代表区间起点，j 代表 2^j ，即小区间长度
for (int i = 1; i <= n; i ++) cin >> f[i][0]; // 每个小区间长度为 1 时
for (int j = 1; j <= 20; j ++) // 枚举区间长度 2^j
	for (int i = 1; i + (1 << j) - 1 <= n; i ++) // 枚举小区间起点,合并后的大区间必须要 <= n
		f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]); // 求最大值(两个小区间的最大值中更大的那个数)

查询代码：
for (int i = 1, l, r; i <= m; i ++) // m 次询问
{
	cin >> l >> r;
	int k = log2(r-l+1); // log2函数在cmath库中，且返回的是对应的浮点数类型，将其赋值给int，只取整数部分
	cout << max(f[l][k], f[r - (1 << k) + 1][k]) << endl;
	// 从前往后和从后往前取两部分，一定能覆盖所求的区间
}
```

预处理：![](assets/Pasted%20image%2020250516154615.png)
	区间终点为： $i + 2^j - 1 \leq n$ 
倍增示例：![](assets/Pasted%20image%2020250516154551.png)