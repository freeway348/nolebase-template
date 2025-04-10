# Flood Fill

---

- 可以在线性时间复杂度内找到某个点所在的连通块

### 做法总结

>[!tip] 特别注意！！！
>在题目描述中，方向为上北下南，左西右东，但根据我们存储的顺序来看，二维数组g[N][N]的第一维g[N]存储的是上北和下南，第二维度g[][N]存储的是左西和右东

注意：在数组输入时，我们常常使用如下形式进行读入
```C++
for (int i = 0; i < n; i ++)
    for (int j = 0; j < n; j ++)
        std::cin >> g[i][j]; // g数组为存储输入信息矩阵的数组
```
在遍历所有联通块时，先查找判断是否已经找过/是否是我们需要的联通块，即：
```C++
bool st[N][N];//全局变量
int cnt = 0;  // 局部变量，在main函数中定义
for (int i = 0; i < n; i ++)
	for (int j = 0; j < n; j ++)
		if (!st[i][j]) // 如果该联通块未被查找过（因为一个联通块要么就没被找过，此时所有小块都是未被查找的状态，即：st[i][j]=false;否则，联通块内所有小块都应该是被查找过的状态，即：st[i][j]=true）
		{
			bfs(i, j);// 从某点开始查找整个联通块
			cnt ++; // 初值为0，用于存储联通块的个数
		}
```
或：
```C++
bool st[N][N];
int cnt = 0;  
for (int i = 0; i < n; i ++)
	for (int j = 0; j < n; j ++)
		if (!st[i][j] && g[i][j] == '?') // 问号处应为特定的字母标记，如：W，G，K，H等，遇到要求的条件才进行联通块查询 
		{
			bfs(i, j);
			cnt ++; 
		}
```

一般在bfs函数中使用数组模拟队列
如：
```C++
// 池塘计数
void bfs(int sx, int sy)
{
    p[0] = {sx, sy};
    int hh = 0, tt = 0;
    st[sx][sy] = true;
    while(hh <= tt)
    {
        PII t = p[hh ++];

        for (int i = t.x - 1; i <= t.x + 1; i ++)
            for (int j = t.y - 1; j <= t.y + 1; j ++)
            {
                if (i == t.x && j == t.y) continue;

                if (i < 1 || i > n || j < 1 || j > m) continue;
                if (g[i][j] == '.' || st[i][j]) continue;
                
                p[++ tt] = {i, j};
                st[i][j] = true;
            }
    }
}
```

### 题目： 

1. Acwing 1097 池塘计数
```C++
#include <iostream>
#include <algorithm>
#include <utility>
#define x first
#define y second
typedef std::pair<int, int> PII;


const int N = 1010, M = N * N;
char g[N][N];
bool st[N][N];
PII p[M]; // 模拟队列  
int n,m;

void bfs(int sx, int sy)
{
    p[0] = {sx, sy};
    int hh = 0, tt = 0;
    st[sx][sy] = true;
    while(hh <= tt)
    {
        PII t = p[hh ++];

        for (int i = t.x - 1; i <= t.x + 1; i ++)
            for (int j = t.y - 1; j <= t.y + 1; j ++)
            {
                if (i == t.x && j == t.y) continue;
                if (i < 1 || i > n || j < 1 || j > m) continue;
                if (g[i][j] == '.' || st[i][j]) continue;
                
                p[++ tt] = {i, j};
                st[i][j] = true;
            }
    }
}


int main()
{

    std::cin >> n >> m;
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= m; j ++)
            std::cin >> g[i][j];
    int cnt = 0;
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= m; j ++)
        {
            if (g[i][j] == 'W' && !st[i][j])
            {
                cnt ++;
                bfs(i, j);
            } 
        }
    std::cout << cnt << std::endl;
    return 0;
}
```