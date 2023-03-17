- `const`修饰普通变量或者成员变量，定义成`const`常量，相较于宏常量可进行类型检查，节省内存空间，提高了效率。
- `const`修饰函数参数，使得传递过来的函数参数的值不能改变。
- `const`修饰成员函数，使得成员函数不能修改任何类型的成员变量（`mutable`修饰的变量除外），也不能调用非`const`成员函数，因为非`const`成员函数可能会修改成员变量。
1. `const`变量：
    定义成`const`常量，相较于宏常量，可进行类型检查，节省内存空间，提高了效率。被定义为`const`的变量是不可修改的。
```cpp
#include <iostream>
using namespace std;

int main()
{
    const int y = 10;
    cout << y;
    y = 9; // error
    return 0;
}
```
2. `const`指针：
	- `const`修饰指针指向的内容，则指针指向的内容不可变，但是指针本身的内容可以改变。
```cpp
int x = 0;
int *q = &x;
const int *p = &x;
*p = 10; // error
p = q; // OK
```
- `const`修饰指针，则指针为不可变量，指针指向的内容可以变，但指针本身不能变。
```cpp
int a = 8;
int* const p = &a; // 指针为常量
*p = 9;  // OK
int  b = 7;
p = &b; // error
```
- `const` 修饰指针和指针指向的内容，则指针和指针指向的内容都为不可变量。
```cpp
int a = 8;
const int * const p = &a;
```
3. `const`引用：
	`const`引用是指向`const`对象的引用，可以读取变量，但不能通过引用修改指向的对象。我们可以将`const`引用指向非`const`变量，但不能使用非`const`引用指向`const`变量。`const`引用可以初始化为不同类型的对象或者右值（如字面值常量），但非`const`引用不可以。
```cpp
int i = 10;
const int &ref = i;
double d = 3.14;
const int &ref2 = d;
```
4. `const`成员变量：
	`const`成员变量只能在类内声明、定义，在构造函数初始化列表中初始化。
	`const`成员变量只在某个对象的生存周期内是常量，对于整个类而言却是可变的，因为类可以创建多个对象，不同类的`const`成员变量的值是不同的。因此不能在类的声明中初始化`const`成员变量。
5. `const`函数参数与返回值：
	用`const`修饰函数参数，表明函数参数为常量，在函数内部不可以修改参数的内容，一般我们使用`const`指针或者`const`引用。函数返回值如果为指针或者引用，我们可以用`const`指针或者引用接受返回值，此时指向的内容则不可以修改。
6. `const`成员函数：
	不能修改成员变量的值，除非有`mutable`修饰；只能访问成员变量。
	不能调用非常量成员函数，以防修改成员变量的值。

- `const`对象只能访问`const`成员函数，而非`const`对象可以访问任意的成员函数，包括`const`成员函数。
- 加上`mutable`修饰符的数据成员，对于任何情况下通过任何手段都可修改，自然此时的`const`成员函数是可以修改它的。
```cpp
#include <iostream>
using namespace std;
class A
{
public:
	int var;
	A(int tmp) : var(tmp) {}
	void c_fun(int tmp) const // const 成员函数
	{
		var = tmp; // error: assignment of member 'A::var' in read-only object. 在 const 成员函数中，不能修改任何类成员变量。		
		fun(tmp); // error: passing 'const A' as 'this' argument discards qualifiers. const 成员函数不能调用非 const 成员函数，因为非 const 成员函数可能会修改成员变量。
	}

	void fun(int tmp)
	{
		var = tmp;
	}
};
int main()
{
    return 0;
}
```

[返回](readme.md)