`lambda`表达式，又被称为`lambda`函数或者`lambda`匿名函数。
`lambda`匿名函数的定义：
```cpp
[capture list] (parameter list) -> return type
{
	function body;
};
```
其中：
- `capture list`：捕获列表，指`lambda`所在函数中定义的局部变量的列表。定义在与`lambda`函数相同作用域的参数引用也可以被使用，一般被称作`closure`（闭包），以下为闭包的常见用法。
```cpp
[]      // 没有定义任何变量。使用未定义变量会引发错误。
[x, &y] // x以传值方式传入（默认），y以引用方式传入。
[&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
[=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
[&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
[=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。
```
比如下面以引用的方式调用`a`：
```cpp
int main()
{
    int a = 10;
    auto f = [&a](int x)-> int {
        a = 20;
        return a + x;
    };
    cout<<a<<endl; // 10
    cout<<f(10)<<endl; // 30
    cout<<a<<endl; // 20
    return 0;
}
```
- `return type`、`parameter list`、`function body`：分别表示返回值类型、参数列表、函数体，和普通函数一样。  
    举例：
```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int arr[4] = {4, 2, 3, 1};
    //对 a 数组中的元素进行升序排序
    sort(arr, arr + 4, [=](int x, int y) -> bool{ return x < y; } );
    auto f = [&](int x)-> int {
        return arr[0] + x;
    }

    for(int n : arr){
        cout << n << " ";
    }
    return 0;
}
```
- 需要注意的是`lambda`函数按照值方式捕获的环境中的变量，在`lambda`函数内部是不能修改的，否则编译器会报错。其值是`lambda`函数定义时捕获的值，不再改变。如果在`lambda`函数定义时加上`mutable`关键字，则该捕获的传值变量在`lambda`函数内部是可以修改的，对同一个`lambda`函数的随后调用也会累加影响该捕获的传值变量，但对外部被捕获的那个变量本身无影响。
```cpp
#include <iostream> 
using namespace std;
int main()
{
	size_t t = 9;
	auto f = [t]() mutable{
		t++;
		return t; 
	};
	cout << f() << endl; // 10
	t = 100;
	cout << f() << endl; // 11
	cout << "t:" << t << endl; // t: 100
	return 0;
}
```
- 引用捕获可能带来悬挂引用常见于使用`lambda`表达式使用引用捕获某个局部变量，而调用`lambda`表达式时，局部变量已经被清理导致捕获的引用指向被清理的内存空间，从而产生悬挂引用。比如下面程序实例中，当`GetFunc`返回时，`s`的对象已经被销毁，此时`s`的引用则会出现问题，应将其修改为值传递。
```cpp
#include <iostream>
#include <cstring>
#include <functional>

auto GetFunc(){
    std::string s = "112234234234";
    return [&](){ std::cout << s << std::endl; };
}

int main(int, char*[]){
    auto func = GetFunc();
    func();
    return 0;
}
```
允许在`lambda`捕获列表中对变量进行表达式赋值，并且支持定义新的变量并进行初始化。
```cpp
auto lambda = [value = 1] {return value;}
```
- `C++ 17`以后，`lambda`函数也支持使用用`constexpr`修饰，此时`lambda`函数内部应当满足`constexpr`的要求。
```cpp
int y = 32;
auto answer = [y]() constexpr
{
    int x = 10;
    return y + x;
};

constexpr int Increment(int n)
{
    return [n] { return n + 1; }();
}
```

[返回](C++关键字与关键库函数/readme)