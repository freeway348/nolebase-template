# day03-基本流程

---

### 一、三种执行顺序

1. 顺序结构
2. 分支结构
3. 循环结构

### 二、分支结构
1. if
2. switch
	1. 通过比较**值相等**来判断分支
格式：
```Java
switch(表达式)
{
	case 值1:
		执行代码；
		break；
	case 值2：
		执行代码；
		break； // 执行完该分支的代码后，通过break跳出switch分支结构
	case ....
	....
	default： // 与所有case值都无法匹配，则进入default
		执行代码； // 此处不用break是因为这里已经是最后一种情况了，执行结束后也会退出switch选择了
}
```
#### 各自的适用场景

- if 在功能上远远比switch强大
- 区间条件时使用if
- 使用**值匹配**时使用switch（格式良好，性能良好，代码优雅）
	- 因为if 会一个一个往下判断完所有else if 和else，但switch会同时判断所有匹配，若有一个匹配则直接进入分支，效率也更高
```
switch 的注意事项和穿透性应用
1. 表达式类型只能是byte、short、int、char，JKD5开始支持枚举，JDK7开始支持String，不支持double，float。long
因为double，float在计算机中无法给出精确值，在计算机中可能会是无穷多位的，而long不允许是因为数值范围太大

2. case 给出的值不允许重复，且只能是字面量，不能是变量
3. 正常使用switch时，不要忘记写break，否则会出现穿透现象
即：执行了某一个分支匹配后，由于没有写break，则会向下穿透下一个case分支并执行其代码，直到遇到下一个break后才会退出

穿透性的应用：如果有多个case分支所需要执行的执行代码语句相同，则可利用该穿透性简化代码，如：case 1，case 3， case 4的执行代码相同，均为System.out.println("Hello");
则代码书写如下：
switch(a)
{
	case 1:
	case 3:
	case 4:
		System.out.println("Hello");
		break;
	case 2:
		System.out.println("GG");
		break;
	default:
		System.out.println("End");
}
```

### 三、循环结构

