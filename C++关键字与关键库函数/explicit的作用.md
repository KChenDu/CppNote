用来声明类构造函数是显式调用的，而非隐式调用，可以阻止调用构造函数时进行隐式转换和赋值初始化。只可用于修饰单参构造函数，因为无参构造函数和多参构造函数本身就是显示调用的，再加上`explicit`关键字也没有什么意义。
隐式转换：
```cpp
#include <iostream>
#include <cstring>
using namespace std;

class A
{
public:
    int var;
    A(int tmp)
    {
        var = tmp;
    }
};
int main()
{
    A ex = 10; // 发生了隐式转换
    return 0;
}
```
上述代码中，`A ex = 10`；在编译时，进行了隐式转换，将10转换成`A`类型的对象，然后将该对象赋值给`ex`，等同于如下操作：
```cpp
A ex1(10);
A ex = ex1;
```
为了避免隐式转换，可用`explicit`关键字进行声明：
```cpp
#include <iostream>
#include <cstring>
using namespace std;

class A
{
public:
    int var;
    explicit A(int tmp)
    {
        var = tmp;
        cout << var << endl;
    }
};
int main()
{
    A ex(100);
    A ex1 = 10; // error: conversion from 'int' to non-scalar type 'A' requested
    return 0;
}
```
- 在日常使用时一般情况下建议声明`explicit`的构造函数，从而可以阻止编译器执行非预期（往往也不被期望）的类型转换，因为某些非预期的类型转换可能会引起意向不到的错误。

[返回](C++关键字与关键库函数/readme)