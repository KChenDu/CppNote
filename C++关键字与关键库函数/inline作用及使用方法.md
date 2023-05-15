`inline`是一个关键字，可以用于定义内联函数。内联函数，像普通函数一样被调用，但是在调用时并不通过函数调用的机制而是直接在调用点处展开，这样可以大大减少由函数调用带来的开销，从而提高程序的运行效率。内联函数可以在头文件中被定义，并被多个`.cpp`文件`include`，而不会有重定义错误。
- 使用方法：类内定义成员函数默认是内联函数，除了虚函数以外，因为虚函数是在运行时决定的，在编译时还无法确定虚函数的实际调用。
	在类内定义成员函数，可以不用在函数头部加`inline`关键字，因为编译器会自动将类内定义的函数（构造函数、析构函数、普通成员函数等）声明为内联函数，代码如下：
	```cpp
	#include <iostream>
	using namespace std;
	
	class A{
	public:
	    int var;
	    A(int tmp){ 
	      var = tmp;
	    }
	    void fun(){ 
	        cout << var << endl;
	    }
	};
	
	int main()
	{    
	    return 0;
	}
	```
- 类外定义成员函数，若想定义为内联函数，需用关键字声明
	当在类内声明函数，在类外定义函数时，如果想将该函数定义为内联函数，则可以在类内声明时不加`inline`关键字，而在类外定义函数时加上`inline`关键字。关键字`inline`必须与函数定义体放在一起才能使函数成为内联，如果只是`inline`放在函数声明前面不起任何作用。
	```cpp
	#include <iostream>
	using namespace std;
	
	class A{
	public:
	    int var;
	    A(int tmp){ 
	      var = tmp;
	    }
	    void fun();
	};
	
	inline void A::fun(){
	    cout << var << endl;
	}
	
	int main()
	{    
	    return 0;
	}
	```

[[C++关键字与关键库函数/readme|返回]]