# set 用法

```C++
// set的底层是红黑树
#include <iostream>
#include <set>
using namespace std;

int main() {
    // 1. 创建set
    set<int> mySet;
    set.clear(); // 清空
    // 2. 插入元素
    mySet.insert(5);
    mySet.insert(3);
    mySet.insert(8);
    mySet.insert(5);  // 因为5已经存在，这个不会生效
    
    // 3. 遍历set元素（自动排序）
    cout << "Set elements: ";
    for(int num : mySet) { // 结构化绑定
        cout << num << " ";  // 输出：3 5 8
    }
    cout << endl;
    
    // 4. 查找元素
    if(mySet.find(5) != mySet.end()) { // 若找到，则返回
        cout << "5 is in the set" << endl;
    }
    
    // 5. 删除元素
    mySet.erase(3);
    
    // 6. 获取大小
    cout << "Size after removal: " << mySet.size() << endl;
    
    return 0;
}

```

- 特点：
	- 自动排序元素
	- 不允许重复元素
	- 查找、插入、删除时间复杂度均为O(log n)
	- 底层实现通常是红黑树