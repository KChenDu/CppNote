`switch`下面的这个花括号表示一块作用域，而不是每一个`case`表示一块作用域。如果在某一`case`中定义了变量，其作用域在这块花括号内，按理说在另一个`case`内可以使用该变量，但是在实际使用时，每一个`case`之间互不影响，是相对封闭的，参考如下实例。
实例：下述代码中，在`switch`的`case`中定义的变量，没有实际意义，仅为了解释上述原因。
```cpp
#include <iostream>
using namespace std;

int main()
{
    // 局部变量声明
    char var = 'D';

    switch (var)
    {
    case 'A':
        int cnt = 0; // 定义变量
        cout << "Excellent." << endl
             << cnt;
        break;
    case 'B':
    case 'C':
        ++cnt;
        cout << "Good." << endl
             << cnt;
        break;
    case 'D':
        cout << "Not bad." << endl
             << cnt;
        break;
    case 'F':
        cout << "Bad." << endl
             << cnt;
        break;
    default:
        cout << "Bad." << endl
             << cnt;
    }

    return 0;
}
```
简单解释：上述代码中在符合`A`的条件下定义了变量，当符合`B`或者`C`的条件时，对该变量进行自增操作，但是因为不符合条件`A`未对变量进行定义，该变量无法使用。

[返回](C++语言特性相关/readme)