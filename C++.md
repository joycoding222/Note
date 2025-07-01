# C++

> 施磊老师课程笔记



### 引用

1. 引用必须初始化，指针可以不进行初始化（引用是更安全的指针）
2. 定义指针变量和引用变量在汇编指令上没有任何区别，一模一样；通过引用变量修改所引用内存的值，
    和通过指针解引用修改指针指向的内存的值，其底层指令是一模一样的
3. 引用只有一级引用，指针可以有多级指针
4. 数组的引用变量的大小和数组一样；而用指针操作数组时指针的大小仅由系统位数决定

### 左值引用和右值引用：

1. 右值引用专门用来引用右值类型，可以自动产生临时量，然后直接引用临时量
2. 右值引用变量本身是一个左值
3. 不能用一个右值引用变量，来引用一个左值

```cpp
/*
C++ 引用

1. 引用必须初始化，指针可以不进行初始化（引用是更安全的指针）
2. 定义指针变量和引用变量在汇编指令上没有任何区别，一模一样；通过引用变量修改所引用内存的值，
    和通过指针解引用修改指针指向的内存的值，其底层指令是一模一样的
3. 引用只有一级引用，指针可以有多级指针
4. 数组的引用变量的大小和数组一样；而用指针操作数组时指针的大小仅由系统位数决定

左值引用和右值引用：
1. 右值引用专门用来引用右值类型，可以自动产生临时量，然后直接引用临时量
2. 右值引用变量本身是一个左值
3. 不能用一个右值引用变量，来引用一个左值

*/
#include <iostream>
using namespace std;

#if 0
int main()
{
    // 正确的引用
    int a = 10;
    int &b = a;

    // 错误的引用
    int &c = 20;    // 20 无法取地址

    return 0;
}
#endif


#if 0
int main()
{
    int array[5]{0, 1, 2, 3, 4};
    // 指针指向数组
    int *ptr = array;

    // 引用数组
    int (&p)[5] = array;

    for (int &ele : array)
    {
        cout << ele << " ";
    }
    cout << endl;

    for (int &ele : p)
    {
        cout << ele << " ";
    }
    cout << endl;

    cout << *p << endl;
    cout << *ptr << endl;
    cout << *array << endl;
    cout << *(p + 1) << endl;
    cout << *(ptr + 2) << endl;
    cout << *(array + 3) << endl;

    // 4.
    cout << "array: " << sizeof(array) << endl;
    cout << "ref: " << sizeof(p) << endl;
    cout << "ptr: " << sizeof(ptr) << endl;

    return 0;
}
#endif

int main()
{
    int a = 10; // a 是左值：有内存 有名字 值可以被修改
    int &b = a; // 左值引用
    int &c = 20;    // 20 是右值：只能放在操作符的右边，没内存，没名字

    // C++11 的右值引用
    int &&c = 20;   // 右值引用

    int &cc = c;    // 一个右值引用变量(c)本身是一个左值

    const int &b = 20;
    /*
    相当于：
    int temp = 20;
    temp -> b (temp的地址给b)
    */

    return 0;
}
```

## 高级课程

1. 右值
2. 智能指针
3. 绑定器、函数对象、lambda表达式
4. C++11内容汇总
5. 设计模式
6. 面向对象编程实践



### 1. 对象的优化

1. 带右值引用的拷贝构造函数：做资源的转移而不是拷贝
2. `std::move`移动语义，将传入参数转换为相应类型的右值
3. `std::forward`类型完美转发，能够识别左值或右值



### 4. C++11

1. auto：根据右值推导变量类型
2. nullptr：空指针（指针专用，值是0）
3. for-each：底层通过指针或迭代器实现

```cpp
std::vector<int> vec;
for (int &ele : vec)
{
    cout << ele;
}
```

4. 右值引用 move forward

5. 模板：typename ...A 表示可变参数

6. 绑定器和函数对象******

7. lambda表达式

8. 智能指针 shared_ptr & weak_ptr

9. 容器

   unorder_set & unorder_map

   array

   forwait_list

10. C++语言级别的多线程
