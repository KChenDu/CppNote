1. `std::move()`函数原型：  `move` 函数是将任意类型的左值转为其类型的右值引用。

首先需要了解一下，引用折叠原理：
- 右值传递给上述函数的形参`T&&`依然是右值，即`T&& &&`相当于`T&&``。
- 左值传递给上述函数的形参`T&&`依然是左值，即`T&& &`相当于`T&`。
我们已经知道折叠原理，通过引用折叠原理可以知道，`move()`函数的形参既可以是左值也可以是右值。
再次详细描述`move`函数的处理流程：
- 首先利用万能模板将传入的参数`t`进行处理，我们知道右值经过`T&&`传递类型保持不变还是右值，而左值经过`T&&`变为普通的左值引用，以保证模板可以传递任意实参，且保持类型不变；对参数`t`做一次右值引用，根据引用折叠规则，右值的右值引用是右值引用，而左值的右值引用是普通的左值引用。万能模板既可以接受左值作为实参也可以接受右值作为实参。
- 通过`remove_refrence`移除引用，得到参数`t`具体的类型`type`；
最后通过`static_cast<>`进行强制类型转换，返回`type &&`右值引用。
2. `remove_reference`主要作用是解除类型中引用并返回变量的实际类型。
```cpp
//举例如下,下列定义的a、b、c三个变量都是int类型
int i;
remove_refrence<decltype(42)>::type a;             //使用原版本，
remove_refrence<decltype(i)>::type  b;             //左值引用特例版本
remove_refrence<decltype(std::move(i))>::type  b;  //右值引用特例版本
```
3. `forward`保证了在转发时左值右值特性不会被更改，实现完美转发。主要解决引用函数参数为右值时，传进来之后有了变量名就变成了左值。比如如下代码：
```cpp
#include <iostream>
using namespace std;

template<typename T>
void fun(T&& tmp) 
{ 
    cout << "fun rvalue bind:" << tmp << endl; 
} 

template<typename T>
void fun(T& tmp) 
{ 
    cout << "fun lvalue bind:" << tmp << endl; 
} 

template<typename T>
void test(T&& x) {
    fun(x);
    fun(std::forward<T>(x));
}

int main() 
{ 
    int a = 10;
    test(10);
    test(a);
    return 0;
}
/*
fun lvalue bind:10
fun rvalue bind:10
fun lvalue bind:10
fun lvalue bind:10
*/
```
参数`x`为右值，到了函数内部则变量名则变为了左值，我们使用`forward`即可保留参数`x`的属性。

`forward`与`move`最大的区别是，`move`在进行类型转换时，利用`remove_reference`将外层的引用全部去掉，这样可以将`t`强制转换为指定类型的右值引用，而`forward`则利用引用折叠的技巧，巧妙的保留了变量原有的属性。以下示例代码就可以观察到`move`与`forward`的原理区别:
```cpp
#include <iostream>
using namespace std;

typedef int&  lref;
typedef int&& rref;

void fun(int&& tmp) 
{ 
    cout << "fun rvalue bind:" << tmp << endl; 
} 

void fun(int& tmp) 
{ 
    cout << "fun lvalue bind:" << tmp << endl; 
} 

int main() 
{ 
    int a = 11; 
	int &b = a;
	int &&c = 100;
	fun(static_cast<lref &&>(b));
	fun(static_cast<rref &&>(c));
	fun(static_cast<int &&>(a));
	fun(static_cast<int &&>(b));
	fun(static_cast<int &&>(c));
    return 0;
}
/*
fun lvalue bind:11
fun rvalue bind:100
fun rvalue bind:11
fun rvalue bind:11
fun rvalue bind:100
*/
```

[返回](C++语言特性相关/readme)