# DFS

---

- 一般用栈（stack）来实现
- 基于连通性的模型都可以用DFS和BFS来做，例如：Flood Fill，图和树的遍历
	- 在内部的某一点能否走到内部的另一个点
- 把整个部分看成一个整体，询问能否将一个整体变成另一个整体
- 注意爆栈问题，DFS的栈深度为1MB，注意不要因为递归层数过大而爆栈
	- 如果会爆栈，则尽可能用迭代的方法来写，或者写成非递归的方式
	- 出现`Segment Fault`就表示爆栈了
### 一、恢复现场的时机

- 不需要恢复现场：如果是处理一个连通性问题，即：在一个整体内部能否走到内部的另一个点，则不需要恢复现场
- 需要恢复现场：如果是一个整体的问题，如：在某一棋盘内，询问哪一点开始才能符合题目要求，则每一行需要遍历每一个点来判断是否合适，则此时，尝试完第一个点后，需要恢复现场，适用于需要**找出所有合适的不同方案**的题目
```C++
// 恢复现场
st[x][y] = true;
dfs(x, y);
st[x][y] = false;
```
- 如果用到了恢复现场，那么在多种输入样例的情况下，就不需要每次都初始化bool判断数组了
### 二、连通性问题

- 既可用DFS，又可用BFS
- DFS可能会爆栈，只能判断是否能够到达，但代码量小；BFS空间占用大，能找到最短路径，代码量大

### 三、可能用到的函数

- $substr(pos, len)$：用于**复制**string数组从pos开始的len个字符
```C++
如：
stirng name = "safe";
string a = name.substr(0, 3);
cout << a;
则会输出：saf


也可以用于判断两字符串的连续子串是否相同
如：下列用于判断字符串 a 的后缀与 b 的前缀相同的最小字符个数
string a = "jsdliawdj";
string b = "jdwialjd";
int ans = -1;
for (int k = 1; k < min(a.size(), b.size()); k ++)
	if (a.substr(a.size() - k, k) == b.substr(0, k))
	{
		ans = k;
		break;
	}
cout << ans;

substr只提供起始位置的用法：
substr会默认从该位置开始获取知道字符串末尾的所有字符:
stirng a = "assasinate"
string b = a.substr(4);
cout << b;
输出结果为："asinate"
```
