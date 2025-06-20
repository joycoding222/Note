# C++ grammar

> 南科大于老师C++课程笔记

## functions

```cpp
// function prototypes 函数声明（函数原型）
int mul(int a, int b);

// funciotn definations
int mul(int a, int b)
{
    return a * b;
}
```

## Compile and Link

1. source code(.c) --(Compile)--> object file (.o)（二进制目标文件）
2. object file(.o) --(Link)--> executable file



## Pre-processor预处理

- begin with #
- execute before compilation



## Output and Input

```cpp
// C++ style output: cout
std::ostream cout;

// input: cin
std::istream cin;
cin >> a;

// C style output
#include <stdio.h>
int num = 10;
printf("value = %d\n", num);

// input
int v;
int ret = scanf("%d", &v);
```



## Command line arguments

```shell
./a.out hello.cpp -o main
# 0: a.out
# 1: hello.cpp
# 2: -o 
# 3: main
```



## Declare & initialize

```cpp
int i;	// declare
int i = 10;	// declare and initialize

// how to initialize
int a;
a = 10;	// way 1
int a = 10;	// way 2
int a (10);	// way 3
int a {10};	// way 4
```

## Data Type

- int

  - unsigned int
    $$
  	-2^{31} \sim 2^{31} - 1
  	$$
  	
  - signed int
  	$$
  	0 \sim 2^{32} - 1
  	$$

- sizeof ： sizeof is an **operator**
- char : 8 bits integer

```cpp
char c1 = 'C';	// ASCII code is 80
char c2 = 80;	// 十进制
char c3 = 0x50;	// 十六进制

 /* signed char 类型的范围是：-128 ~ 127
  * unsigned char 类型的范围是：0 ~ 255
  */
```

- bool **is a C++ keyword, not C**
  - width: 1 bytes(8 bits) 
  - value: true or false
- size_t: unsigned int
- float: 
  - 除了不显示末尾的0之外，浮点类型float总共显示6位数字；
  - 浮点数用定点计数法或者指数计数法

## operator & operation

1. const
2. auto：从初始化值推到变量类型

```cpp
 auto a = 2; // type of a is int
 auto bc = 2.3; // type of b is double
 auto c ; //valid in C, error in C++
 auto d = a * 1.2; 
```

3. Division

```cpp
float f = 17 / 5;	// f will be 3.f, not 3.4f

float f = 17 / 5.f;	// f will be 3.4f
```



## Lab02

C++ 提供了两种控制输出格式的方法：

- 使用ios类的成员函数
- 使用<iomanip>中的操作符

```cpp
// output format
// use member function of ios class
cout.width(len);	// set field width
cout.fill(ch);		// ch to be used to justified field
cout.precision(p);	// set the precision of floating number
cout.setf();
cout.unsetf();

cout << 56.8 << endl;	// 56.8

cout.width(12);
cout.fill('+');
cout << 456.77 << endl;	// +++++++456.8

// use manipulators in <iomanip>
// #include <iomanip>
// 这些操作符会自动调用setf()，并设置正确的参数
bool flag = false;
double a = 2.3876;
double b = 0.46e2;
cout << boolalpha << flag << endl;   // false
cout << fixed << a << endl;          // 2.387600
cout << b << endl;                   // 46.000000
cout << noboolalpha << flag << endl; // 0
cout.unsetf(ios::fixed);
cout << a << endl; // 2.3876
cout << b << endl; // 46
```

### 类型转换

隐式类型转换规则：

- 整数提升：char short 等小于 int 的类型会先提升为 int
- 类型等级提升：
  - 若操作数中存在浮点数，向更高精度的浮点类型转换
  - 若操作数中无浮点数，向更宽范围的整数类型转换

```cpp
// Lab02 exercise05.cpp
#include <iostream>
int main()
{
    auto a = 10; // 10
    std::cout << a << std::endl;

    a = 20.5; // 20
    std::cout << a << std::endl;

    a += 10.5; // 30.5
    std::cout << a << std::endl;

    int b = 20 + 10.5;
    std::cout << b << std::endl;
    return 0;
}
/*
    计算过程中发生了什么：
    1. 首先a被推断为int类型；
    2. 20.5被转换为int类型后，赋值给a；
    3. 首先计算20 + 10.5 = 30.5，并且结果为double类型，再隐式类型转换为int类型后，赋值给a；
*/
```

### 数据溢出

```cpp
// Lab02 exercise01.cpp
#include <stdio.h>
int main()
{
    signed char a = 127;    
    unsigned char b = 0xff;
    unsigned char c = 0;

    a++;
    b++;
    c--;
    printf("a=%d\nb=%d\nc=%d\n", a, b, c);
    return 0;
}
/**
 * a = 0111 1111, 输出-128
 * b = 1111 1111, 输出0
 * c = 0000 0000, 输出255，原因：向前借位， 1 0000 0000 - 1 = 0 1111 1111
 * 
 * signed char 类型的范围是：-128 ~ 127
 * unsigned char 类型的范围是：0 ~ 255
 */
```



### 大数表示

```cpp
// Lab02 exercise02.cpp
#include <stdio.h>
using namespace std;

int main()
{
    long long int a = 56789 * 23456789ll;	// 结尾加ll表示longlongint
    printf("%d\n", __INT32_MAX__);
    printf("a=%lld\n", a);
    printf("%ld\n", sizeof(long long int));
    return 0;
}
/**
 * C 输出大数字：
 * long long int 按理来说是64位，足够表示；
 * 在计算大数字时，为避免数字超过int类型表示的范围，可以在数字后加上ll，指定类型为long long int;
 */
```

### 浮点数比较

```cpp
// Lab02 exercise03.cpp
#include <iostream>
using namespace std;
int main()
{
    cout << fixed;
    cout.precision(12); // 补充设置小数点后12位精度
    float f1 = 1.0f;
    cout << "f1 = " << f1 << endl;

    float a = 0.1f;
    float f2 = a + a + a + a + a + a + a + a + a + a;
    cout << "f2 = " << f2 << endl;

    if (f1 == f2)
        cout << "f1 = f2" << endl;
    else
        cout << "f1 != f2" << endl;

    return 0;
}
/**
 * 将浮点数精度设置位小数点后12位，f1和f2的打印值不同
 */
```

## 2. DataStructure基本数据结构

### 2.1 Array数组

- 数组是一块连续的内存，它的元素类型可以是任何基本元素类型(int float bool)以及结构体、类、指针、枚举

```cpp
// 数组初始化
int num[5];	// 未初始化，数组元素是随机值
int num[5] = {0, 1, 2, 3, 4};	// 声明并初始化
int num[] = {1, 2, 3, 4};		// 长度未知的数组，num的类型是4个int的数组
```

- 当将数组作为参数传入函数:

```cpp
float arraySum(float values[], size_t length);
float arraySum(float *value, size_t length);
```

- 注意：在C/C++中，**数组没有越界检查**，并且数组不是对象
- 数组的存储：

![001数组存储](D:\zhangjy\coding\note\C++基础图片\001数组存储.jpg)

### 2.2 MultiDimensional arrays多维数组

- 多维数组的初始化与遍历

```cpp
// 初始化
int mat[2][3] = {{1, 2, 3}, {4, 5, 6}};
// 遍历
for (int i = 0; i < row; ++i)
{
    for (int j = 0; j < col; ++j)
    {
        cout << mat[i][j] << endl;
    }
}
```

- 多维数组的存储：从下图可以理解，多维数组在声明时，必须指定列数（每行多少个元素）

  ![002多维数组存储](D:\zhangjy\coding\note\C++基础图片\002多维数组存储.jpg)

### 2.3 string字符串

**Array style string数组风格的字符串**

- 数组风格的字符串是一些列的字符，以字节为单位存储

```cpp
// array style string initialize数组风格字符串初始化
char rabbit[16]  = {'P', 'e', 't', 'e', 'r'};
char bad_pig[9] = {'P', 'e', 'p', 'p', 'a', ' ', 'P', 'i', 'g’}; 
//a bad one!因为存储的字符元素个数和字符串长度一致，没有结束符\0
char good_pig[10] = {'P', 'e', 'p', 'p', 'a', ' ', 'P', 'i', 'g', '\0'};

// 不指定字符串长度
char name3[] = "ABCD";	// 内存中占用5字节
```

- 字符串的长度：`strlen`属于库`<cstring>`，返回字符的个数，以第一个`\0`结尾

```cpp
int main()
{
    char name[10] = {'Y', 'u', '\0', 'S', '.', '0'};
    cout << strlen(name) << endl;   // <cstring>    output: 2

    return 0;
}
```

- 字符串的操作函数`<cstring>`

```cpp
// 操作函数
char*strcpy(char* dest, const char* src);	// copy

char* strncpy(char* dest, const char* src, size_t count);	// copy

char *strcat(char* dest, const char* src);	// 连接

int strcmp(const char* lhs, const char* rhs);	// 比较
```

**string class字符串类**

- 没有终止符`\0`的字符串很容易越界，并且引发其他问题；因此C++提供了string类

```cpp
std::string str1 ="Hello";
std::string str2 = "C++";
std::string result = str1 + str2;	// 重载了+运算符
```

### 2.4 Struct结构体

- 结构体是一系列成员的组合，在内存中成员按照顺序存储

```cpp
 struct Student1{
 	int id;
 	bool male;
 	char label;
 	float weight;
 };
 Student1 stu;
 stu.id = 123;
```

- C++中不需要typedef
- 结构体的存储：为了对齐内存中的数据，将会填充一些空字节（灰色）

![003结构体存储](D:\zhangjy\coding\note\C++基础图片\003结构体存储.jpg)

### 2.5 Union联合体

- union的声明和结构体很相似，但是联合体的成员变量共享内存空间（成员变量的首地址相同）

### 2.6 enum枚举

- 枚举类型

```cpp
enum class color {RED, WHITE, BLACK};
color pen_color = RED;
```

### 2.7 typedef

- 创建一个类型的别名

```cpp
// 定义了一个名为vec的int类型数组的别名，它表示一个包含3个int元素的数组
typedef int vec[3];

// typedef
    vec num = {0, 2, 3}; // 相当于int num[3] = {0, 2, 3};
    for(int i = 0; i < 3; ++i)
    {
        cout << num[i] << " ";
    }
```

## 3. 程序输入输出控制

> 内容参考Lab04

### 3.1 输入

cin：以空格/tab/换行为间隔

```cpp
    cin >> str;                            // hello world
    cout << "Your enter: " << str << endl; // hello
//  The cinis to use whitespace--spaces, tabs, and newlinesto separate a string.
```

cin.get()

```cpp
#include <iostream>
using namespace std;

int main(){
    char str[20];

    cout << "Enter:" << endl;
    cin.get(str, 20);
    cout << "your enter:" << str << endl;

    cin.get();  //加上这一行才能继续读入，否则只会打印"Enter" & "your enter"
    cout << "Enter:" << endl;
    cin.get(str, 20);
    cout << "your enter:" << str << endl;

    return 0;
}
```

使用cin.getline()替换cin.get()，可以避免上述情况

**区别是cin.getline()丢掉了换行符**

```cpp
/*Difference between cin_get() and cin_getline():
 getline() and get()both read an entire input line—that is, up until a newline character. 
 However, getline() discard the newline character, whereas get() leave it in the input queue*/
```

### 3.2 strlen VS sizeof

- strlen()用于计算字符串（字符数组）的长度，需要包含头文件<string.h>，**需要以'\0'结尾**

## 4. 指针

> 内容参考[Lecture05.pdf](file:///D:/zhangjy/coding/projects/CPP-main/CPP-main/week05/Lecture05.pdf)

### 4.1 指针的声明和初始化

```cpp
int *p1 = NULL;	// 声明p1指针，并且初始化为0

// C风格的打印地址
printf("address of var: %p\n", &var);
```

### 4.2 常量指针***

```cpp
int num = 1;
int another = 3;

// 第一种：
// 不能通过p1指针修改num的值
const int *p1 = &num;
*p1 = 2;	// 错误！！
num = 2;	// 正确

// 第二种：
// p2指针的值不能被修改
int * const p2 = &num;
*p2 = 2;		// 正确
p2 = &another;	// 错误！！！

// 第三种：
// 既不能...也不能...
const int * const p3 = &num;
```

### 4.3 指针和数组***

- 数组名可以看作指向首元素的指针（如对于int数组，数组名的类型为int *）
- 区别：数组是 const pointer

```cpp
int i = 1;
int *p = ...;	// int数组

// 等价
p[i] = 3;
*(p + i) = 3;

// 区别
int num[4] = {0, 1, 2, 3};
int *p = num;
cout << sizeof(num) << endl;	// 4 * sizeof(int)
cout << sizeof(p)  << endl;		// 4 or 8

// 重点！！！！！
int b[4] = {0, 1, 2, 3};
int *ptr2 = (int *)b + 1;	// 正确
int *ptr3 = &b + 1;			// 错误！！！因为&b的类型是int(*)[4]
// 二维数组中：
int a[][4] = {0, 1, 2, 3, 4, 5, 6, 7};	// a 的类型是int(*)[4]， 指向第一行
a + 1		// 指向第二行
*(a + 1)	// 指向第二行首元素
```

***辨析b和&b!!!!!!***

b是数组名，也是数组首元素地址，类型为**int ***

&b是数组的地址，类型为**int(*)[4]**

### 4.4 内存申请和释放

- 程序的地址空间包括：代码、数据、BSS（未初始化的静态数据包括变量和常量）、堆（动态内存）、栈（局部变量）

```cpp
// C 风格申请内存
int *p1 = (int*)malloc(4);	// 申请4字节
free(p1);	// 释放p1

// C++ 风格，{}是初始化列表
int *p1 = new int;		// 申请1个int大小，默认初始化
int *p1 = new int(); 	// 申请1个int大小，初始化为0
int *p1 = new int(5);	// 初始化为5
int *p1 = new int{};	// 申请1个int大小，初始化为0， C++11
int *pa1 = new int[16]{1, 2, 3};	// 申请16个int
delete p1;
delete []pa1;	// 释放数组内存，并调用第一个元素的析构函数
delete pa1;		// 释放数组内存，并调用所有元素的析构函数
```

## 5. 函数

> 内容参考lecture06

函数的参数传递方式有两种：

1. 值传递 pass by value：参数是原变量的拷贝
2. 引用传递 pass by reference

### 5.1 引用

- 引用是已存在变量或对象的别名，引用是C++的概念
- 引用在声明时必须初始化

```cpp
int num = 10;
int & num_Ref = num;

// 在函数调用中引用传递参数
float matrix_max(const struct Matrix &mat)
{
	//...函数体
}
```

### 5.2 返回值

### 5.3 内联函数

- 什么是内联函数：**编译器尝试将函数调用替换为函数体本身的代码**，从而避免函数调用的开销（函数调用需要堆栈操作，保存程序当前的运行状态等等）

- 为什么要使用：频繁调用某些函数是很大的内存开销
- `inline`关键字建议编译器进行某些优化

**内联函数的工作原理：**

1. **编译时处理（而非运行时）**
2. 尝试替换代码
3. 非强制

**为什么不使用宏函数：**

1. 宏函数无类型检查
2. 存在多次求值问题
3. **不方便调试（预处理阶段进行替换）**

## 6. 函数重载

> 内容参考lecture07

### 6.1 参数默认值

在函数声明中可以给参数指定默认值

```cpp
float norm(float x, float y = 0, float z = 1.0f);
```

### 6.2 函数重载

不能仅依赖返回值类型区分重载函数



### 6.3 函数模板

- 函数模板不是函数或任一实体，仅包含函数模板的源文件不生成代码
- 模板参数必须被确定，然后编译器才生成相应函数

```cpp
template<typename T>
T sum(T x, T y)
{
    return x + y;
}

// 显式实例化
template double sum<double>(double, double);

// 隐式实例化
cout << sum(2.2f, 3.0f);

// 特例化

// function template
template <typename T>
int Compare(const T &a, const T &b)
{
    cout << "The input type is " << typeid(a).name() << endl;
    if (a > b)
        return 1;
    else if (a < b)
        return -1;
    else
        return 0;
}

// define a struct
struct stuinfo
{
    string name;
    int age;
};

// function specialization 函数特例化
template <>
int Compare(const stuinfo &a, const stuinfo &b)
{
    cout << "The input type is " << typeid(a).name() << endl;
    if (a.age > b.age)
        return 1;
    else if (a.age < b.age)
        return -1;
    else
        return 0;
}
```

### 6.4 函数指针

```cpp
// 函数声明
float norm_l1(float x, float y);

// 函数指针norm_ptr
float (*norm_ptr)(float x, float y);

norm_ptr = norm_l1;	// 函数指针指向norm_l1
float len1 = norm_ptr(3.0f, 4.0f);	// 函数调用

norm_ptr = &norm_l1;// 函数指针指向norm_l1
float len2 = (*norm_ptr)(3.0f, 4.0f);
```

函数指针可以作为一个参数传递给其他函数：
```cpp
// 排序
void qsort( void *ptr, size_t count, size_t size,
 int (*comp)(const void *, const void *) )
```



### 6.5 函数引用

```cpp
float norm_l1(float x, float y); //declaration
float norm_l2(float x, float y); //declaration
float (&norm_ref)(float x, float y) = norm_l1; //norm_ref is a function referenc
```

### 6.6 递归函数

优点：代码量小

缺点：栈开销大，难以调试

## 7. 类和对象

### 7.0 类 VS 结构体

类的初始化：

**推荐使用初始化列表C++11**

类和结构体对比：

1. 可以通过关键字`private`保护某些成员变量，这些成员变量只能被成员函数访问，更加安全
2. 当通过类创建对象时，会分配内存并调用类的构造函数；当创建结构体变量时，仅仅会分配内存；

```cpp
// 结构体
struct Student
{
	char name[4];
    int born;
    bool male;
};

// 类
class Student
{
public:
    char name[4];
    int born;
    bool male;
};
```



### 7.1 构造函数Constructor

特点：

1. 和类的名称相同；
2. 没有返回值；
3. 公开成员函数；
4. 当创建该类的成员时调用；
5. 当没有提供构造函数时，编译器会自动生成一个默认无参数的构造函数

注意：
**构造函数可以被重载**




### 7.2 析构函数Destructor

当类的对象生命周期结束被销毁时，析构函数将会被调用

特点：

1. ~
2. 没有返回值
3. 没有参数
4. 一个类只能有一个析构函数
5. 如果没有提供，编译器会默认生成
6. 当对象作用域结束，或指向对象的指针被delete，调用析构函数

注意：
**析构函数不能被重载**



### 7.3 this 指针

this 指针：调用某方法的**对象的地址**

```cpp
void setBorn(int born)
{
    this->born_ = born;
}
```



### 7.4 const & static 成员

1. const成员变量和普通的const变量类似
2. const成员函数不修改成员变量的值，**定义在类的外部的const成员函数在声明和定义时都必须指定const关键字**
3. **C++ 标准规定：如果类包含 `const` 成员或引用成员，编译器将删除默认的拷贝赋值运算符**

```cpp
class Student
{
private:
	const int BMI = 24;
    
public:
	int getBorn() const
    {
        return born;
	}
};
```

3. static
- 静态成员和类的实例不绑定，即被static修饰的变量是类实例之间共享的，在内存中只有一个副本，不管有多少个对象。

- 静态成员变量必须在类的外部初始化

- 静态成员函数可以不依赖实例调用

- 一个成员函数可以被声明作为静态成员函数，当且仅当它不访问类的任何非静态成员变量

```cpp
/**
 * static 关键字：
 * 1. static 关键字修饰的成员变量的类的实例共享同一副本
 * 2. 必须在类的外部初始化
 */
class MyClass
{
public:
    static int count;
    
    int x;
    int y;

public:
    MyClass()
    {
        ++count;
    }

    MyClass(int x, int y)
    : x(x)
    , y(y)
    {
        ++count;
    }

    ~MyClass()
    {
        --count;
    }

    // getCount
    static int getCount()
    {
        return count;
    }
};

int MyClass::count = 0;

int main()
{
    MyClass task1;
    MyClass task2(1, 2);
    MyClass task3{};

    cout << task1.getCount() << endl;
    cout << MyClass::count << endl;
    cout << MyClass::getCount() << endl;    // 静态成员函数无需实例化即可使用

    return 0;
}
```




### 7.5 static 关键字

`static`关键字主要控制变量和函数的声明周期、作用域和链接属性。

用法：

1. 静态局部变量

```cpp
void counter() {
    static int count = 0; // 静态局部变量
    count++;
    cout << "Count: " << count << endl;
}

int main() {
    counter(); // 输出 Count: 1
    counter(); // 输出 Count: 2
    counter(); // 输出 Count: 3
}

/*
特点：
生命周期：整个程序运行期间
作用域：仅限于函数内部
初始化：首次进入函数时，初始化一次
内存位置：
数据段
*/
```

2. 静态成员变量（类内部）：

```cpp
class Player {
public:
    static int totalPlayers; // 声明静态成员变量
    
    Player() { totalPlayers++; }
    ~Player() { totalPlayers--; }
};

int Player::totalPlayers = 0; // 类外定义（必需！）

int main() {
    Player p1;
    Player p2;
    cout << Player::totalPlayers; // 输出 2
}

/*
特点：
1. 类的所有实例共享同一副本
初始化：
2. 必须在类的外部单独初始化！！！！！
*/
```

3. 静态成员函数（类内部）

```cpp
class MathUtils {
public:
    static int add(int a, int b) {
        return a + b;
    }
    
    // 错误：静态函数不能访问非静态成员
    // static void error() { cout << value; } 
private:
    int value;
};

int main() {
    int sum = MathUtils::add(5, 3); // 无需实例化
}

/*
特点：
1. 无this指针：静态成员函数不能访问非静态成员；
2. 可以通过类名直接调用
*/
```



### 7.6 运算符重载

```cpp
class MyTime
{
private:
	int hours;
    int minutes;
public:
	MyTime(): hours(0), minuters(0){}
    
    MyTime operator+(const MyTime & t) const
    {
		MyTime sum{};
        sum.minutes = this->minutes + t.minutes;
        sum.hours = this->hours + t.hours;
        sum.hours += sum.minutes / 60;
        sum.minutes %= 60;
        return sum;
   	}
    std::string getTime() const;
}

// 运算符重载的形式：
t1 + 20;
t1.operator+(20);
// 不支持的形式
20 + t1
```

**运算符type()重载**

```cpp
// implicit conversion
operator int() const
{
	// ...
}

// explicit conversion
explicit operator int() const
{
	// ...
}
```

**实例分析**

```cpp
MyTime t2 = 80;	// 拷贝构造函数
MyTime t3(80);	// 参数化构造函数
MyTime t4;		// 默认构造函数
t4 = 80;		// 拷贝赋值运算符
```



### 7.7 友元函数friend functions

- 在类的内部声明
- 有权访问类的成员（包括私有成员）
- 但友元函数**不是类的成员函数**，只是在类的内部声明

```cpp
class MyTime
{
private:
	int hours;
    int minutes;
public:
	MyTime(): hours(0), minuters(0){}
    
    MyTime operator+(const MyTime & t) const
    {
		MyTime sum{};
        sum.minutes = this->minutes + t.minutes;
        sum.hours = this->hours + t.hours;
        sum.hours += sum.minutes / 60;
        sum.minutes %= 60;
        return sum;
   	}
    std::string getTime() const;
    
    // friend function: declare and define
    friend MyTime operator+(int m, const MyTime & t)
    {
		return t + m;
    }
    
    // or declare in the class
    friend MyTime operator+(int m, const MyTime & t);
}

// define out of the class
MyTime operator+(int m, const MyTime & t)
{
	return t + m;
}

// 使用 friend function 重载流式运算符
friend std::ostream & operator<<(std::ostream & os, const MyTime & t)
{
	//...
}
```

为什么使用友元函数重载`<<`运算符：

如果输出自定义的类的成员，需要对输出运算符进行重载；

但又不能去修改输出运算符其源文件`iostream`

因此可以在类中使用友元函数



### 7.8 **返回对象时的注意事项*****

> 参考 Lab 10 讲义

1. 当成员函数返回对象时，可以返回对象，或者对象的引用
2. 当返回对象时，一个临时对象被创建，需要额外**调用拷贝构造函数**，比较低效，当函数调用结束后，临时对象被销毁；
3. 返回对象的引用时，则不需要；
4. **不要返回一个临时对象的引用**，因为当临时对象被销毁，引用将会指向不存在的对象（返回`*this`）
5. **return value optimization:** 将构造函数作为返回参数，这种方式编译器可以消除创建临时对象的花销

```cpp
Complex Complex::operator+(const Complex& rhs)
{
	double re = real + rhs.real;
    double im = imag + rhs.imag;
    
   	return Complex(re, im);	// return value optimization
}
```



### 7.9 类中的动态内存问题（深浅拷贝）

> 参考 Lecture 11 & Lab11

​	在包含了指针成员的类中，应该尤其注意**构造函数、析构函数、拷贝构造函数、拷贝赋值运算符**。

1. 默认构造函数default constructor：没有任何参数的构造函数

2. 拷贝构造函数：仅仅有一个参数，其他参数均为默认值；如果用户没有定义，编译器会自动生成一个默认拷贝构造函数，拷贝所有的**非静态成员**

   何时调用拷贝构造函数：

   - 当一个类的对象被值返回(return by value)；
   - 当一个对象值传递(pass by value)给一个函数调用；
   - 当一个对象从另一个相同类的对象处被创建(`MyClass o1 = o2`)
   - 当一个临时对象被编译器生成；

   ```cpp
   // copy constructors
   MyTime::MyTime(MyTime &t)
   {}
   
   // invoke copy constructor
   MyTime t2(t1);
   MyTime t3 = t1;
   MyTime t4 = MyTime(t1);
   MyTime *ptr = new MyTime(t1);
   ```


3. 拷贝赋值运算符：如果没有定义，编译器自动生成，拷贝所有**非静态成员**

   ```cpp
   // 重载=
   MyTime & MyTime::operator=(MyTime &)
   {}
   
   // examples
   MyTime t1(1 ,59);
   MyTime t2 = t1;	// copy constructor
   t2 = t1;		// copy assignment
   ```

**深浅拷贝：**

当需要进行拷贝操作（拷贝构造函数和拷贝赋值运算符），有两种操作：

1. 深拷贝 deep copy
   - 深拷贝创建数据的完整副本，并且独占所有权，修改时只影响当前对象，内存效率较低，适用于需要独立修改的数据；
   - 对于 copy constructor 需要申请自己的内存；
   - 拷贝赋值运算符结合了拷贝构造和析构函数的操作。拷贝赋值运算符释放掉运算符左边对象的资源（类似析构函数的操作），从右边对象处拷贝数据（类似拷贝构造函数的操作）。

```cpp
class PtrHardCopy
{
private:
	string *ps;
    int i;
    
public:
	PtrHardCopy(const string &s = string())	// 构造函数
        : ps(new string(s))
        , i(0){}
    PtrHardCopy(const string &p)			// 深拷贝构造函数
        : ps(new string(*p.ps))	// 先拿到p.ps指针，再解引用，再用它来初始化新申请的内存
        , i(p.i){}
    PtrHardCopy& operator=(const PtrHardCopy& rhs)	// 拷贝赋值重载
    {
        // 释放左边对象的资源
		auto newp = new string(*rhs.ps);
        delete ps;	// 释放原内存
        // 从右边对象拷贝数据
        ps = newp;	// ps指向新申请的内存地址
        i = rhs.i;
        return *this;
    }
    ~PtrHardCopy(){delete ps;}
};
```

2. 浅拷贝 shallow copy

   - 浅拷贝只复制指针，共享底层数据，资源共享，修改时所有对象都会受到影响；

   - 增加了新的成员变量`size_t* num`用于追踪有多少个对象共享了同样的内存地址

   - 为什么多个对象可以共享引用计数：因为num是指向引用计数个数的指针，在拷贝构造和拷贝赋值时，同时赋值给了新对象

```cpp
class PtrSoftCopy
{
private:
	string* ps;
    int i;
    size_t* num;	// 计数
public:
	PtrSoftCopy(const string& s = string())	// 构造函数
        : ps(new string(s))
        , i(0)
        , num(new size_t(1)){}	// 初始化为1，表示有一个对象使用string成员
    PtrSoftCopy(const PtrSoftCopy& p)	// 拷贝构造函数
        : ps(p.ps)
        , i(p.i)
        , num(p.num){++*num;}	// 计数加1
    PtrSoftCopy& operator=(const PtrSoftCopy& rhs)
    {
		++*rhs.num;	// 增加右侧对象的引用计数
        
        if(--*num == 0)	// 减少左侧对象的原引用计数
        {
            // 左侧对象没有其他对象使用，则释放左侧对象资源
			delete ps;
            delete num;
        }
        ps = rhs.ps;
        i = rhs.i;
        num = rhs.num;
        return *this;
    }
    ~PtrSoftCopy()
    {
		if (--*num == 0)
        {
			delete ps;
            delete num;
        }
    }
};
```

3. 深拷贝 VS 浅拷贝

   区别：

   浅拷贝只复制指针，共享同一数据；

   深拷贝创建完整的数据副本；



### 7.10 智能指针

智能指针和普通指针类似，除了会自动进行资源回收。一个智能指针是一个类模板定义在std命名空间内的`<memory>`头文件内。

**std::shared_ptr**

1. 确保当一个对象不被使用时再释放它；

2. 几个共享指针可以指向同一对象；

3. 初始化`shared_ptr`之后，可以拷贝，可以通过值传递，可以复制给其他的实例；

4. 使用`Ref_count`进行引用计数，可以使用`use_count()`获取引用；

5. 当最后一个指针被释放时（引用计数为0），共享内存被释放；

   ```cpp
   // shared_ptr
   // 使用构造函数的方式创建
   std::shared_ptr<MyTime> mt1(new MyTime(10));
   std::shared_ptr<MyTime> mt2 = mt1;	// mt2 也指向new的对象
   
   // 使用make_shared创建
   std::shared_ptr<MyTime> mt1 = std::make_shared<MyTime>(1, 70);
   
   // 查看引用计数
   cout << mt1.use_count() << endl;
   ```

**std::unique_ptr**

1. 指向一个对象，并且不允许其他指针指向该对象；

2. 需要改变指向的对象时，需要使用`std::move()`。即两个`unique_ptr`的赋值操作是不被允许的，但支持移动语义，可以使得`unique_ptr`指向另一个对象。

3. 创建时可以用一个裸指针来初始化。

   ```cpp
   // unique_ptr
   // 使用构造函数创建
   std::unique_ptr<MyTime> mt1(new MyTime(10));
   
   // 使用make_unique创建
   std::unique_ptr<MyTime> mt2 = std::make_unique<MyTime>(80);	// c++17
   
   // 使用std::move()改变指向
   std::unique_ptr<MyTime> mt3 = std::move(mt1);
   ```


**使用`shared_ptr`的注意事项：**

1. 注意尽量不要使用局部变量的裸指针来初始化智能指针，容易引起double free问题；

   ```cpp
   string str("Hello World!");
   shared_ptr<string> pstr(&str);	// error!!!局部变量str作用域结束后自动释放，如果使用智能指针会造成内存重复释放问题
   shared_ptr<string> pstr(new string("Hello World!"));	// Recommend
   cout << "*pstr = " << *pstr << endl;
   ```



## 8. 派生类

### 8.1 继承

- 继承：从一个类继承成员（属性和函数）。

- C++支持多重继承和多级继承。

```cpp
class Base
{
public:
	int a;
    int b;
};

// 继承
class Derived: public Base
{
public:
    int c;
};

// 多重继承
class Derived: public Base1, public Base2
{
    //...
};
```

- 构造函数：

  当实例化一个派生类的对象：

  - 首先调用基类的构造函数（按照继承顺序）
  - 调用成员对象的构造函数（按照声明顺序）
  - 调用派生类自身的构造函数

- 析构函数：

  - 首先调用派生类的析构函数
  - 然后调用基类的析构函数



**派生类对象实例化的构造函数调用过程：**

1. 基类构造函数的调用：

如果派生类的构造函数没有显式调用基类构造函数，编译器会自动调用基类的默认构造函数；

要调用基类的带参构造函数，必须在派生类的初始化列表中显式指定；

```cpp
class Base {
public:
    Base() { cout << "Base default constructor\n"; }
    Base(int x) { cout << "Base(" << x << ") constructor\n"; }
};

class Derived : public Base {
public:
    // 自动调用 Base::Base()
    Derived() { cout << "Derived constructor\n"; }
    
    // 显式调用 Base::Base(int)必须在初始化列表中显式指定
    Derived(int x) : Base(x) { 
        cout << "Derived(" << x << ") constructor\n"; 
    }
};
```

2. 成员对象的构造：

成员对象按照他们在类中**声明的顺序**构造，而不是初始化列表中的顺序；

如果没有在初始化列表中指定，会调用成员对象的默认构造函数；

```cpp
class Base {
public:
    Base() { cout << "Base default constructor\n"; }
    Base(int x) { cout << "Base(" << x << ") constructor\n"; }
};

class Member {
public:
    Member() { cout << "Member default constructor\n"; }
    Member(int y) { cout << "Member(" << y << ") constructor\n"; }
};

class Derived : public Base {
    Member m1;  // 先声明
    Member m2;  // 后声明
public:
    // 构造顺序：Base() → m1() → m2() → Derived()
    Derived() { cout << "Derived constructor\n"; }
    
    // 构造顺序：Base(x) → m1(y) → m2() → Derived()
    Derived(int x, int y) : Base(x), m1(y) {
        cout << "Derived(" << x << "," << y << ") constructor\n"; 
    }
};
```

3. 多继承情况下的构造顺序

存在多个基类时，基类按照**继承声明的顺序**构造；

与派生类初始化列表中的顺序无关；

```cpp
class Base1 {
public:
    Base1() { cout << "Base1 constructor\n"; }
};

class Base2 {
public:
    Base2() { cout << "Base2 constructor\n"; }
};

// 构造顺序：Base1 → Base2 → Derived
class Derived : public Base1, public Base2 { // 继承声明顺序
public:
    Derived() { cout << "Derived constructor\n"; }
};

// 即使交换初始化列表顺序，构造顺序不变
class Derived2 : public Base2, public Base1 {
public:
    // 实际构造顺序：Base2 → Base1 → Derived2
    Derived2() : Base1(), Base2() { 
        cout << "Derived2 constructor\n"; 
    }
};
```

**关键规则总结：**

1. 构造顺序：
   - 基类（按照继承声明顺序）
   - 成员对象（按照声明顺序）
   - 派生类对象自身
2. 析构顺序：与构造完全相反
3. 注意：
   - 多态基类的析构函数应该为虚函数



公有继承代表了`is-a`关系，即每一个派生类对象是一个基类对象。



### 8.2 访问控制

Public成员：可以随意访问；

Private成员：只能被类的成员和友元访问

Protected成员：类的成员和友元可以访问，派生类的成员和友元可以访问

公开继承：

- 基类的公有成员在派生类中仍然公有
- 可以随意访问
- 受保护成员在派生类中仍然受保护
- 仅可以在派生类中访问
- 基类的私有成员在派生类中不可访问

保护继承：

- 基类的公有成员和保护成员在派生类中是保护成员；
- 仅可以在派生类中访问；
- 基类的私有成员在派生类中不可访问；

私有继承：

- 基类的公有成员和保护成员在派生类中是私有成员
- 仅可以在派生类中访问；
- 基类的私有成员在派生类中不可访问

总结：

受保护的成员变量可以被：基类的成员函数、基类的友元函数、派生类的成员函数访问；

私有的成员变量可以被：基类的成员函数、基类的友元函数访问；

公开的成员变量可以被：到处访问；

### 8.3 虚函数

虚函数：C++多态的核心机制，允许在运行时根据对象的实际类型调用正确的函数实现（动态绑定）。

静态绑定：编译器决定调用哪个函数

动态绑定：运行时决定调用哪个函数



1. 虚函数定义与声明

```cpp
class Animal {
public:
    // 虚函数声明
    virtual void makeSound() const {
        cout << "Generic animal sound" << endl;
    }
    
    // 纯虚函数（抽象方法）
    virtual void move() const = 0;
};

class Dog : public Animal {
public:
    // 重写虚函数
    void makeSound() const override {
        cout << "Woof! Woof!" << endl;
    }
    
    // 实现纯虚函数
    void move() const override {
        cout << "Running on four legs" << endl;
    }
};
```

2. 虚函数工作原理

   - 虚函数表：每个包含虚函数的类都有一个虚函数表vtable，编译器自动生成，存储类中所有虚函数的指针，每个对象包含指向虚函数表的指针vptr

   - 调用过程：

     通过基类指针找到vptr，通过vptr找到虚函数表vtable，通过vtable找到虚函数的入口，调用实际指向的函数

```cpp
Animal* animal = new Dog();
animal->makeSound(); // 调用 Dog::makeSound()
```

3. 虚函数怎么用
   - 多态基类的析构函数声明为虚函数
   - 派生类中使用`override`明确重写方法





# Tricks：

1. C++禁止引用非常量的临时变量；

   > 参考博客：[C++禁止引用非const的临时变量 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/165391845)

2. 不能返回临时变量的引用；其实就是说：临时变量不能和非const的引用绑定;

3. 拷贝构造函数不能按值传递(pass by value)其自身的类型（会无限递归）；

# 附录：Linux Commands

Linux commands is a program or utility that runs on the command line interface(a console that interacts with the system via texts and processes).

## Linux Commands Directory

1. pwd 
2. cd
3. ls； ll; show the detail information of subdirectory and files
4. mkdir
5. rmdir <directory name> 删除没有文件的目录
6. cat <filename> 显示文件内容
7. rm; -f: allow the system to remove without confirmation ; -r: delete files and directories recursively
8. cp <source> <dest> 复制文件
9. mv <source> <dest> 移动文件
