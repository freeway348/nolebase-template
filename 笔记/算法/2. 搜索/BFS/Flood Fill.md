# Flood Fill

---

- 可以在线性时间复杂度内找到某个点所在的连通块

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