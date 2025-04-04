# BFS适用

---

- BFS性质：
	1. 宽搜（BFS）第一次搜索就能搜索到最小值，所以如果是要求“最小”，则选用BFS
	2. BFS是基于迭代的搜索算法，**不会爆栈**
- BFS好处：当所有边的权重都相等时，用宽搜从起点开始搜，可以得到所有点的单源最短路（所有点到起点的**最短路**）


在C++中，将数组`PII q[M]`定义为全局变量或局部变量（如放在`bfs`函数内部）的区别主要体现在内存分配、作用域和适用场景上：

### **1. 内存分配方式**
- **全局变量**：  
  数组`q`在**静态存储区**分配内存，程序启动时即存在，生命周期持续到程序结束。  
  静态存储区空间较大（通常与程序内存限制相关），能容纳大数组（如本题中`M = N*N = 1e6`）。

- **局部变量**：  
  若将`q`定义在`bfs`函数内部，它会在**栈空间**分配内存。  
  栈空间较小（通常约几MB），大数组（如`1e6`元素）会导致**栈溢出**（Stack Overflow），程序崩溃。

---

### **2. 作用域与数据隔离性**
- **全局变量**：  
  所有函数均可访问`q`，存在潜在的多函数/多线程竞争风险。  
  但在此代码中，`bfs`每次调用会重置`hh`和`tt`，实际不会残留数据，复用安全。

- **局部变量**：  
  仅在`bfs`函数内可见，不同调用间数据完全隔离，避免意外修改，增强封装性。  
  但受限于栈大小，无法直接用于大数组。

---

### **3. 适用场景**
- **全局变量**：  
  **推荐用于大数组**（如本题的`M = 1e6`）。  
  避免栈溢出，且通过合理设计（如每次调用重置指针）可安全复用内存。

- **局部变量**：  
  仅适用于**小规模数组**（如`M ≤ 1e4`）。  
  需确保数组大小在栈容量内，适合简单场景或递归深度可控的情况。

---

### **4. 替代方案**
若需保持局部变量的封装性，同时避免栈溢出，可使用**动态内存分配**：  
```cpp
void bfs(...) {
    PII* q = new PII[M];  // 堆区分配
    // ... 使用q ...
    delete[] q;           // 释放内存
}
```
或使用`std::vector`（推荐）：
```cpp
void bfs(...) {
    std::vector<PII> q(M); // 堆区分配，自动管理内存
    // ... 使用q.data()或迭代器 ...
}
```

---

### **结论**
在本题中，由于`M = 1e6`较大，必须将`q`设为全局变量或动态分配，否则局部变量会导致栈溢出。若数组较小（如`M ≤ 1e4`），局部变量更安全且封装性更好。实际开发中，优先使用`std::vector`兼顾灵活性和安全性。