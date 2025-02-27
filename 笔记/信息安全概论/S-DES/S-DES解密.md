# S-DES解密

---

解密过程是加密过程的逆操作。在 S-DES 解密中，最关键的是反向执行每一步，并在解密过程中以相反的顺序使用子密钥 `K1` 和 `K2`。具体的解密步骤如下：

### 1. **密钥生成**

在解密过程中，子密钥 `K1` 和 `K2` 的生成方式与加密时完全相同。对于密钥 `1010000010`，我们之前已经计算出：
- `K1 = 10100100`
- `K2 = 01000011`

### 2. **逆初始置换 (IP-1)**

首先对密文 `10100100` 进行逆初始置换 (IP-1)。假设 IP-1 表如下：

```
IP-1: 4 1 3 5 7 2 8 6
```

我们将密文 `10100100` 进行逆初始置换，得到：

```
逆初始置换结果：00101100
```

然后，将结果分为两部分：`L2 = 0010` 和 `R2 = 1100`。

### 3. **第一轮解密**

解密的第一轮使用的是子密钥 `K2 = 01000011`（注意解密时子密钥的使用顺序与加密相反）。在此轮解密中，我们的输入是 `L2 = 0010` 和 `R2 = 1100`。

#### 步骤 3.1: **扩展置换 (E/P)**

对 `R2 = 1100` 进行扩展置换，假设 E/P 表如下：

```
E/P: 4 1 2 3 2 3 4 1
```

将 `1100` 进行扩展置换，得到：

```
扩展置换结果：01101001
```

#### 步骤 3.2: **与 `K2` 异或**

将扩展置换的结果与子密钥 `K2 = 01000011` 进行异或操作：

```
01101001 XOR 01000011 = 00101010
```

#### 步骤 3.3: **S盒代换**

将结果 `00101010` 分成两部分：`0010` 和 `1010`，分别送入 S1 和 S2 进行代换。

##### S盒1 代换：
`0010` 对应 S1 表中的行 `00` 和列 `10`，查表得到 `10`。

##### S盒2 代换：
`1010` 对应 S2 表中的行 `10` 和列 `10`，查表得到 `00`。

所以 S盒代换的输出为 `1000`。

#### 步骤 3.4: **P4 置换**

将 S盒代换的结果 `1000` 进行 P4 置换，假设 P4 表如下：

```
P4: 2 4 3 1
```

置换结果为：

```
P4置换结果：0001
```

#### 步骤 3.5: **与 `L2` 异或**

将 P4 置换的结果与 `L2 = 0010` 进行异或操作：

```
0001 XOR 0010 = 0011
```

得到 `L1 = 0011`，并且 `R1` 保持为 `1100`。

#### 步骤 3.6: **左右交换**

此时我们交换左右两部分，得到：

```
L1 = 1100, R1 = 0011
```

### 4. **第二轮解密**

第二轮解密中，使用的是子密钥 `K1 = 10100100`。

#### 步骤 4.1: **扩展置换 (E/P)**

对 `R1 = 0011` 进行扩展置换：

```
扩展置换结果：10010110
```

#### 步骤 4.2: **与 `K1` 异或**

将扩展置换后的结果与子密钥 `K1 = 10100100` 进行异或操作：

```
10010110 XOR 10100100 = 00110010
```

#### 步骤 4.3: **S盒代换**

将 `00110010` 分为两部分：`0011` 和 `0010`，分别送入 S盒进行代换。

##### S盒1 代换：
`0011` 对应 S1 表的行 `11` 和列 `01`，查表得到 `01`。

##### S盒2 代换：
`0010` 对应 S2 表的行 `00` 和列 `10`，查表得到 `10`。

所以 S盒代换的输出是 `0110`。

#### 步骤 4.4: **P4 置换**

将 `0110` 进行 P4 置换，结果为：

```
P4 置换结果：1010
```

#### 步骤 4.5: **与 `L1` 异或**

将 P4 置换结果与 `L1 = 1100` 进行异或操作：

```
1010 XOR 1100 = 0110
```

最终得到 `L0 = 0110`，而 `R0 = 0011` 保持不变。

### 5. **初始置换 (IP)**

将 `L0 = 0110` 和 `R0 = 0011` 合并为 `01100011`，然后对其进行初始置换 (IP)。假设 IP 表如下：

```
IP: 2 6 3 1 4 8 5 7
```

将 `01100011` 置换后，得到明文：

```
明文：10101010
```

### 总结

通过上述步骤，我们将密文 `10100100` 进行了 S-DES 解密，最终恢复出明文 `10101010`。整个解密过程是加密过程的逆向执行，只需将密钥 `K1` 和 `K2` 使用的顺序对调，同时依次进行相反的操作即可。