1. `new`的简介：`new`是`C++`中的关键字，尝试分配和初始化指定或占位符类型的对象或对象数组，并返回指向对象 (或数组的初始对象) 的指针。
- 用`new`创建对象时，首先从堆中申请相应的内存空间，然后调用对象的构造函数，最后返回指向对象的指针。`new`操作符从自由存储区（`free store`）上为对象动态分配内存空间。自由存储区是`C++`基于`new`操作符的一个抽象概念，凡是通过`new`操作符进行内存申请，该内存即为自由存储区。而堆是操作系统中的术语，是操作系统所维护的一块特殊内存，用于程序的内存动态分配。`new`可以指定在内存地址空间创建对象，用法如下：
```cpp
new (place_address) type
```
对于指定的地址的`new`对象，在释放时，不能直接调用`delete`，应该先调用对象的析构函数，然后再对内存进行释放。比如以下程序：
```cpp
include <iostream>
using namespace std;

int main(int argc, char* argv[])
{
    char buf[100];
    int *p=new (buf) int(101);
    cout<<*(int*)buf<<endl;
    return 0;
}
```