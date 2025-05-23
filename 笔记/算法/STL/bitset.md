# bitset
## `bitset`（位集）的基本用法

`bitset` 是用于处理固定大小位序列的类模板，特别适合需要高效位操作的情况。
- bitset是从右往左，从0开始，假设
```C++
bitset<8> bs;  // bs 一共有8位，从右往左标号0~7
bs[7] = 1; // 将第七位设置为 1
```

### 时间复杂度

bitset的时间复杂度为$O(\frac{n}{w})$，w为电脑位数，可用于优化set可能超时的情况

### 基本操作示例

```cpp
#include <iostream>
#include <bitset>
using namespace std;

int main() {
    // 1. 创建bitset（8位）
    bitset<8> b1("11001100");
    bitset<8> b2(0b00111100);  // 二进制字面量
  
    cout << "b1: " << b1 << endl;  // 11001100
    cout << "b2: " << b2 << endl;  // 00111100
  
    // 2. 位操作
    cout << "NOT b1: " << ~b1 << endl;
    cout << "b1 AND b2: " << (b1 & b2) << endl;
    cout << "b1 OR b2: " << (b1 | b2) << endl;
    cout << "b1 XOR b2: " << (b1 ^ b2) << endl;
  
    // 3. 访问和设置位
    cout << "b1[2]: " << b1[2] << endl;  // 0
    b1.set(2);  // 将第2位置为1（从右数，0开始）
    cout << "After set(2): " << b1 << endl;  // 11001110
  
    // 4. 统计
    cout << "b1 has " << b1.count() << " bits set" << endl; // count返回位集中 1的个数
    cout << "Is any bit set? " << b1.any() << endl; // 是否有1
    cout << "Are all bits set? " << b1.all() << endl;// 是否全为1
  
    // 5. 转换
    cout << "As unsigned long: " << b1.to_ulong() << endl; 
    cout << "As string: " << b1.to_string() << endl;
  
    return 0;
}
```

### 特点
- 固定长度位序列
- 高效的位级操作（与、或、非、异或等）
- 直接支持位访问和修改
- 可以转换为数字类型或其他字符串表示
- 适用于需要紧凑存储和快速位操作的应用

## 典型应用场景对比

| 场景 | `set` | `bitset` |
|------|-------|----------|
| 存储唯一值集合 | ✓ 适合 | × 不适合 |
| 排序需求 | ✓ 自动排序 | × 无序 |
| 需要位掩码 | × 不适合 | ✓ 很适合 |
| 元素查找 | ✓ O(log n) | ✓ O(1) |
| 成员检查 | ✓ find() | ✓ test() |
| 集合运算 | ✓ 有并集、交集等方法 | 需要手工位操作 |
| 内存效率 | × 相对高开销 | ✓ 极高效 |

当需要处理数字的集合且元素唯一时优先考虑`set`，当需要高效的位级操作时优先考虑`bitset`。