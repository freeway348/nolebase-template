# Floyd算法

---

```
Dijstra是基于贪心的算法
SPFA和Bellman-Ford是基于DP的算法
floyd也是基于DP的算法
```

### 用处

1. 最短路
2. 传递闭包
3. 找最小环（一般对于正权图来说，找一个和最小的环）
4. 恰好经过`k`条边的最短路（倍增）
- 可处理负边权，但无法处理负环
#### 最短路

```C++
// 牛的旅行
#include <bits/stdc++.h>
using namespace std;
#define x first
#define y second
typedef pair<int, int> PII;
// #define int long long
const int N = 160;
const double INF = 1e20;
PII p[N];          // 存储每个牧区的坐标
char g[N][N];      // 邻接矩阵，表示牧区之间的连接关系
int n;             // 牧区数量
double dist[N][N], // 存储两点之间的最短距离
    maxd[N];       // 存储每个牧区到同一牧场内其他牧区的最远距离

// 计算两点之间的欧几里得距离
double getDis(PII a, PII b)
{
    int dx = a.x - b.x;
    int dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
}

void floyd()
{
    // 初始化距离矩阵
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (i != j)
                if (g[i][j] == '1') // 如果i和j直接相连
                    dist[i][j] = getDis(p[i], p[j]);
                else
                    dist[i][j] = INF; // 初始为无穷大

    // Floyd算法核心：动态更新最短路径
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);

    // 计算每个牧区在其连通块内的最远距离
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (dist[i][j] < INF) // 如果点 i 和点 j 是相互联通的，即在同一联通块内
                maxd[i] = max(maxd[i], dist[i][j]); // maxd[i] 存储的是，在同一联通块内，距离点 i 最远的距离

    // 问题1：结果至少是两个原牧场直径的较大值
    double res1 = 0;
    for (int i = 0; i < n; i++)
        res1 = max(res1, maxd[i]);

    // 问题2：连接两个牧场后可能形成的新直径
    double res2 = INF;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            if (dist[i][j] >= INF) // 如果i和j不在同一连通块
                res2 = min(res2, getDis(p[i], p[j]) + maxd[i] + maxd[j]); // 假设将点 i 和点 j 链接，判断这两点链接后的直径

    // 最终结果是两种情况中的较大值
    printf("%lf", max(res1, res2));
}

void solve()
{
    cin >> n;
    for (int i = 0; i < n; i++)
        cin >> p[i].x >> p[i].y;
    for (int i = 0; i < n; i++)
        cin >> g[i];
    floyd();
}

int main()
{
    solve();
    return 0;
}

```


#### 传递闭包

- 若有向图中，已知：$A\rightarrow B,B\rightarrow C$，则可额外引一条线使得 $A\rightarrow C$ ，将一切能间接表示的都直接相连，此时的状态即为传递闭包，使用一个bool数组来表示，如：$d[i][j]=1$ 表示 $i\rightarrow j$ ，$d[i][j]=0$ 表示 $i\nrightarrow j$ 
- 一般使用一个数组存储原值，用另一个数组进行memcpy复制后，再对该复制数组进行操作判断
- 可以在$O(n^3)$ 的时间复杂度下，完成传递闭包
例题：
```C++
// 排序
// 输入多组样例，样例为如下形式：
/*
A < B
B < C
C < D
xx < xxx
*/*
#include<bits/stdc++.h>
using namespace std;

#define N 30
int st[N], d[N][N], g[N][N];
int n, m;

void floyd() {

    memcpy(d, g, sizeof g);
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                d[i][j] |= d[i][k] && d[k][j]; // 为什么要取或？防止当 k 变化时，d[i][j] 已存在的状态改变

}

int check() {

    for (int i = 0; i < n; i++)//判断是否出现矛盾情况
        if (d[i][i])return 2;

    for (int i = 0; i < n; i++)//判断两两之间关系是否确定
        for (int j = i + 1; j < n; j++)
            if (!d[i][j] && !d[j][i])return 0;

    return 1;
}

char get_min() {

    for (int i = 0; i < n; i++) {
        int flag = 0;
        if (!st[i]) {
            for (int j = 0; j < n; j++) {
                if (!st[j] && d[j][i]) { // 若第 j 个字母未被输出，则说明它小于第 i 个字母，若 d[j][i] 也存在，则说明 j < i,故 i 不是最小的数
                    flag = 1;
                    break;
                }               
            }
            if (!flag) { // 若 i 是最小数，则输出
                st[i] = 1;
                return 'A' + i;
            }
        }
    }
}

char str[5];
int main(){
    int t;//记录轮次
    while (cin >> n >> m, n || m) {
        memset(g,0,sizeof g);
        int type=0;//记录答案类型
        for (int i = 1; i <= m; i++) {
            cin >> str;
            int a = str[0] - 'A', b = str[2] - 'A';

            if(!type){
                g[a][b] = 1;
                floyd();
                type=check();  
                if(type)t=i;
            } 

        }
        if (type == 2)printf("Inconsistency found after %d relations.\n", t);
        else if (!type)printf("Sorted sequence cannot be determined.\n");
        else {
            memset(st, 0, sizeof st);
            printf("Sorted sequence determined after %d relations: ", t);
            for (int i = 0; i < n; i++)
                printf("%c", get_min());
            printf(".\n");
        }

    }

    return 0;
}

```