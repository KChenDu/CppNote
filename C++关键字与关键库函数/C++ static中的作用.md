在`C++`中，使用`static`可以定义局部静态变量、外部静态变量、静态函数、静态成员变量和静态成员函数。因为`C++`中有类的概念，静态成员变量、静态成员函数都是与类有关的概念。
1. `static`全局静态变量：
	- 普通全局变量和`static`全局静态变量都为静态存储方式。普通全局变量的作用域是整个源程序，当一个源程序由多个源文件组成时，普通全局变量在各个源文件中都是有效的；
	- 静态全局变量则限制了其作用域，即只在定义该变量的源文件内有效，在同一源程序的其它源文件中不能使用它。由于静态全局变量的作用域限于一个源文件内，只能为该源文件内的函数公用，因此可以避免在其他源文件中引起错误。静态全局变量只初始化一次，防止在其他文件中使用。实际上全局静态变量存储在内存的静态存储区，生命周期贯穿于整个程序运行期间。静态变量会被放在程序的静态数据存储区，这样可以在下一次调用的时候还可以保持原来的赋值，静态变量用`static`告知编译器，自己仅仅在变量的作用范围内可见，实际是依靠编译器来控制作用域。
2. `static`局部静态变量：局部静态变量只能被初始化一次。与全局静态变量不同的是静态局部变量的作用域仅限于函数内部，它的作用域与函数内部的局部变量相同。实际上局部静态变量同样也存储在静态存储区，因此它的生命周期贯穿于整个程序运行期间。
	- `a.cpp`
		```cpp
		#include <iostream>
		using namespace std;
		int g_var = 0; // 全局变量
		static char *gs_var;  // 静态全局变量
		
		int main()
		{
		    int var;                    // 局部变量
		    static int s_var = 0;       // 静态局部变量
		    return 0;
		}
		```
	- `b.cpp`
		```cpp
		#include <iostream>
		using namespace std;
		extern int g_var = 0; // 访问全局变量
		// extern static char *gs_var;  无法访问静态全局变量
		
		int test()
		{
		    g_var = 1;
		}
		```
3. `static`静态函数：`static`函数限制函数的作用域，仅可在定义该函数的文件内部调用，如下所示程序示例编译会报错，找不到`test`函数定义。
	- `a.cpp`
		```cpp
		#include <iostream>
		using namespace std;
		int g_var = 0; // 全局变量
		static char *gs_var;  // 静态全局变量
		extern int test();
		int main()
		{
		    int var;                    // 局部变量
		    static int s_var = 0;       // 静态局部变量
		    test();
		    return 0;
		}
		```
	- `b.cpp`
		```cpp
		#include <iostream>
		using namespace std;
		
		static int test()
		{
			return 0;
		}
		```
4. `static`静态成员变量：
	- 静态成员变量是在类内进行声明，在类外进行定义和初始化，在类外进行定义和初始化的时候不要出现`static`关键字和`private`、`public`、`protected`访问规则。
	- 静态成员变量相当于类域中的全局变量，被类的所有对象所共享，包括派生类的对象，且只能该变量只能被初始化一次，不能在类的构造函数中对静态成员变量进行初始化。
	```cpp
	// C++ program to demonstrate static
	// variables inside a class
	#include<iostream>
	using namespace std;
	
	class GfG
	{
	public:
		static int i;
		GfG() {
			
		};
	};
	
	int GfG::i = 1; // initial
	
	int main()
	{
	    GfG obj1;
	    GfG obj2;
	    obj1.i =2; // error
	    obj2.i = 3; // error
	    GfG::i = 10; // assignment
	    // prints value of i
	    cout << obj1.i<<" "<<obj2.i; // 10 
	}
	```
	- 静态成员变量可以作为成员函数的参数，而普通成员变量不可以。
		```cpp
		#include <iostream>
		using namespace std;
		
		class A
		{
		public:
		    static int s_var;
		    int var;
		    void fun1(int i = s_var); // 正确，静态成员变量可以作为成员函数的参数
		    void fun2(int i = var);   //  error: invalid use of non-static data member 'A::var'
		};
		int main()
		{
		    return 0;
		}
		```
	- 静态数据成员的类型可以是所属类的类型，而普通数据成员的类型只能是该类类型的指针或引用。
		```cpp
		#include <iostream>
		using namespace std;
		
		class A
		{
		public:
		    static A s_var; // 正确，静态数据成员
		    A var;          // error: field 'var' has incomplete type 'A'
		    A *p;           // 正确，指针
		    A &var1;        // 正确，引用
		};
		
		int main()
		{
		    return 0;
		}
		```
5. `static`静态成员函数：
	静态成员函数不能调用非静态成员变量或者非静态成员函数，因为静态成员函数没有`this`指针。静态成员函数做为类作用域的全局函数。
	静态成员函数不能声明成虚函数（`virtual`）、`const`函数和`volatile`函数。
	```cpp
	#include<iostream>
	using namespace std;
	class GfG
	{
	public:
		static void printMsg()
		{
			cout<<"Welcome to GfG!";
		}
	};
	int main()
	{
		GfG::printMsg();
	}
	```
6. `static`对象：
	静态对象的生存周期为整个程序的生命周期，而非静态对象的生命周期只存在于某个循环中，我们可以通过以下程序做对比：
```cpp
#include<iostream>
using namespace std;

class GfG1
{
	int i;
	public:
		GfG1()
		{
			i = 0;
			cout << "GFG1 Inside Constructor\n";
		}
		~GfG1()
		{
			cout << "GFG1 Inside Destructor\n";
		}
};

class GfG2
{
	int i;
	public:
		GfG2()
		{
			i = 0;
			cout << "GfG2 Inside Constructor\n";
		}
		~GfG2()
		{
			cout << "GfG2 Inside Destructor\n";
		}
};

int main()
{
	int x = 0;
	if (x==0)
	{
		GfG1 obj1;
        static GfG2 obj2;
	}
	cout << "End of main\n";
}
```

[返回](C++关键字与关键库函数/readme)