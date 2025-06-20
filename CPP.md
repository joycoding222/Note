# C++

C++学习记录，用于记录遇到的知识点



## 构造函数

用于初始化对象的特殊成员函数

### 默认构造函数default constructor

定义：

没有参数或所有参数都有默认值的构造函数

作用：
创建对象时，若未提供参数，自动调用

语法：

```cpp
class MyClass {
public:
    MyClass() = default;  // 显式默认构造函数（C++11）
    // 或隐式生成（如果未定义其他构造函数）
};

// 调用默认构造函数
Myclass obj1;
Myclass obj2{};		// C++11统一初始化语法，明确创建对象
```

特点：

- 若用户未定义**构造函数**，编译器会自动生成一个隐式的默认构造函数；
- 若定义了**其他构造函数**，但需要保留默认构造函数，需要显式声明` = default`

**常见问题：**

```cpp
class Box
{
};

Box first(5, 6, 7);	// 调用参数化构造函数
Box second();		// 函数声明！！而非创建对象！！！！
Box third;			// 调用默认构造函数
Box fourth{};		// 调用默认构造函数
```



### 参数化构造函数

定义：接受参数的构造函数

作用：通过参数初始化对象的成员变量

语法：
```cpp
class MyClass {
    int x, y;
public:
    MyClass(int a, int b) : x(a), y(b) {}  // 参数化构造函数
};
```

### 拷贝构造函数copy constructor

定义：
接受同类型对象的引用作为参数的构造函数

作用：

用一个已有对象，来初始化新的对象（深拷贝或浅拷贝）

语法：
```cpp
class MyClass {
public:
    MyClass(const MyClass& other) { /* 拷贝逻辑 */ }
};
```

示例：
```cpp
MyClass obj1;
MyClass obj2 = obj1;  // 调用拷贝构造函数
```

### 移动构造函数move constructor

定义：

接受同类型对象的右值引用，作为参数的构造函数



### 委托构造函数delegating constructor

定义：
一个构造函数可以调用同类型的其他构造函数

### 删除构造函数

` = delete`

作用：
禁止特定的构造函数的调用

语法：

```cpp
class NonCopyable {
public:
    NonCopyable() = default;
    NonCopyable(const NonCopyable&) = delete;  // 禁用拷贝构造
};

class NoDefault {
public:
    NoDefault() = delete;  // 禁用默认构造
};
```

示例：
```cpp
NonCopyable a;
// NonCopyable b = a;  // 错误：拷贝构造函数被删除

// NoDefault c;        // 错误：默认构造函数被删除
```

### 继承构造函数

派生类直接继承基类的构造函数



## String

`std::string`是C++标准库中表示和操作字符串的类

### 底层实现

- 通过动态数组`vector<char>`
- 允许直接访问底层数据

## 操作

```c
// 声明与赋值
string s1;	
string s2 = "Hello";
string s3 = s2;	// 拷贝构造
s1 = "HelloWorld";

// 修改
s2.append("World");	// 在s2后拼接
s2.insert(5, "C++");	// 在s2的第5个(下标5)位置插入"C++"
s2.erase(5, 4);	// 在s2第5个下标位置删除4个字符

// 访问
char c = s2[0];	// 通过下标访问
s2.front();	// 访问首字符
s2.back();	// 访问尾字符

// 容量管理
s2.reserve(100);	// 预分配内存
s2.shrink_to_fit();	// 释放多余内存

// 通过STL迭代器遍历
for (auto it = s2.begin(); it != s2.end(); ++it)
{
    cout << *it;
}

// STL 算法
sort(s2.begin(), s2.end());	// 排序
reverse(s2.begin(), s2.end());	// 反转
```

snprinft函数



## 流式输入

```cpp
// 优雅写法 (推荐)
string s;
while (cin >> s) {
    // ...处理代码... 
}
```

1. 循环发生了什么：读取字符串
2. 何时结束循环：遇到文件结束符EOF、输入流错误、输入流关闭等
3. **注意**：`cin >> s`以制表符、换行符、空格作为分隔符

```bash
# 输入
hello world Ctrl+Z	# 循环执行2次
```

4. 如果需要整行读取：采用`getline(cin, s)`

```cpp
string s;
while (getline(cin, s)) {  // 读取整行（含空格）
    // 处理包含空格的完整行
    cout << "完整行: " << s << endl;
}
```

## 虚函数

虚函数是c++实现多态和接口抽象的经典方式。

```cpp
class Task
{
public:
	virtual void run() = 0;	// 纯虚函数声明
}
```

1. 抽象基类：
   - 不能实例化，即本身不能创建对象，因为包含纯虚函数
   - 强制派生类必须实现特定功能
   - 多态：通过基类指针/不同派生类实现
2. 关键字：
   - virtual: 虚函数关键字（运行时多态，即动态绑定）
   - override: 派生类重写虚函数方法

```cpp
class MyTask: public Task{
    void run() override	// 派生类重写虚函数
    {
        // 具体实现
	}
}
```

## Lambda表达式

Lambda表达式是C++11引入的一种**创建匿名函数对象**的简介方式。

### 语法

```cpp
[capture](parameters) mutable -> return_type {
    // 函数体
}
```

