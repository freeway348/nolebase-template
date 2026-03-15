# Pytest
### 一、Pytest框架介绍

Pytest是一个功能强大且灵活的Python测试框架，广泛用于**单元测试**、**功能测试**和**集成测试**
[Pytest官网](https://docs.pytest.org/en/stable/)
特点：
1. 自动搜索测试用例
2. 支持参数化测试，fixture等高级功能
	- 参数化测试：测试步骤相同且测试数据有多组时，可以允许用**不同的输入数据**多次运行同一个测试方法，而不需要为每组数据编写重复的测试代码。
	- fixture：可重用的代码块
3. 详细的测试失败报告，便于调试
4. 有丰富的插件生态，可扩展功能

### 二、如何使用Pytest

- Pytest的`测试文件`通常以`test_*.py`命名，也可用`*_test.py`命名，测试函数仅以`test`开头，如`def test_login()`，`def test_*()`
	- 若只在命令行运行`pytest`命令，则会扫描所有`test_*.py`和`*_test.py`文件，找到以`Test`开头的**类**和以`test_`开头的**函数**，并执行以`test_`开头的**函数**
fixture：是Pytest为测试提供的`可重用`的代码功能，可以使用scope控制的作用域
1. 定义fixture：\@pytest.fixture修饰函数，说明下面的函数是fixture代码块
	![](assets/Pasted%20image%2020260315112830.png)
2. 应用fixture：只要传入fixture的函数名即可使用
	![](assets/Pasted%20image%2020260315112856.png)
