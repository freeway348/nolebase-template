# 求First集和Follow集

---

### 一、求Fitst集

```
First集中可以包含空串
```
- 求开头：
	1. 步骤一：把非终结符列出，从左往右看
	2. 步骤二：以第一个产生式右侧为起始，先找到First集合中的所有终结符，再判断**非终结符**
		- First集无 $\varepsilon$ ：First集合相同，如：S $\rightarrow$ PQc，则 $First(S)=First(P)$
		- First集有 $\varepsilon$ ：列完该First集后再列下一个的First集，循环直到找到第一个不含 $\varepsilon$ 的First集或终结符，如：S $\rightarrow$ PQcE，且 P 的First集含有 $\varepsilon$ ，则将 $First(P)-\{\varepsilon\}$ 列入First(S)，再判断 Q 的First集是否满足条件：
			- 若无 $\varepsilon$，则将$First(Q)$的元素加入First(S)中，并终止循环；
			- 若有 $\varepsilon$，则将$First(Q)$的元素加入到First(S)中后，再继续往后循环，遇到终结符`c`时，将该终结符也加入First(S)中，终止循环
			- 如果是 S $\rightarrow$ ABCD的情况，若A，B，C，D的`First集`中均含有 $\varepsilon$，则在将A，B，C，D的First集，如：$First(A)-\{\varepsilon\}$ 都加入First(S)后，将 $First(D)$也加入First(S)，因为是最后一个了，所以无法再去除 $\varepsilon$ 以继续循环，就此终止
### 二、求Follow集

- 简单情况不讨论，仅讨 $A\rightarrow aB\beta$ 且 $\beta\Rightarrow\varepsilon$ （$\beta$ 能最终推导出 $\varepsilon$），例如：$\beta\rightarrow ABCD....$ ，则$First(\beta)=First(ABCD....)$ 如果First(A)中存在 $\varepsilon$ ，则将 $First(A)-\{\varepsilon\}$ 加入到FOLLOW（B）中后，再继续判断First(B)，如果First(B)中也存在 $\varepsilon$，则将$First(A)-\{\varepsilon\}$也加入到FOLLOW(B)中，再继续往后循环，如果最终推导出来仍存在 $\varepsilon$，则将所有$First-\{\varepsilon\}$加入到FOLLOW(B)中后，还需要将FOLLOW(A)也加入到FOLLOW(B)中