1. `std::thread`：我们可以通过`thread`创建一个线程（`C++ 11`以后才支持`thread`标准库），`thread`在构造相关线程对象完成后立即开始执行（实际需要等待`OS`对于线程的调度延迟）。`thread`需要配合与`join`或者`detach`配合使用，不然可能出现不可预料的后果。
	- `join`代表阻塞当前主线程，等待当前的`join`的子线程完成后主线程才会继续；
	- `detach`表明当前子线程不阻塞主线程，且与主线程分离，子线程的运行不会影响到主线程。
	```cpp
	#include <iostream>
	#include <thread>
	#include <string>
	using namespace std;
	void fn2(string st2) {
	    cout<<st2<<endl;
	}
	
	void fn1(string st1) {
	    cout<<st1<<endl;
	}  
	
	int main()
	{
	    thread thr1(fn1, "111111111\n"); 
	    thread thr2(fn2, "222222222\n"); 
	    return 0;
	}
	```
	如在一个类中间，一个成员函数需要异步调用另一个函数的时候，需要绑定`this`：
	```cpp
	class Test{
	    private:
	    void func1(const std::string s1, int i) {
	        std::cout << s1 << std::endl;
	    }
	    public:
	    void func2() {
	            std::thread t1(&Test::func1, this, "test", 0);
	            t1.detach();
	            std::cout << "func2" << std::endl;
	    }
	}
	```
2. `std::async`：我们进行异步编程时，需要得到子进程的计算结果，常见的手段是我们可以通过共享变量或者消息队列的方式告知另一个线程当前的计算结果，但是操作和实现都比较麻烦，同时还要考虑线程间的互斥问题。`C++11`中提供了一个相对简单的异步接口`std::async`，通过这个接口可以简单地创建线程并通过`std::future`中获取结果，极大的方便了`C++`多线程编程。`std::async`适合与需要取得结果的异步线程。`C++11`中的`std::async`是个模板函数。`std::async`异步调用函数，在某个时候以`Args`作为参数（可变长参数）调用函数，无需等待函数执行完成就可返回，返回结果是个`std::future`对象。函数返回的值可通过`std::future`对象的`get`成员函数获取。一旦完成函数的执行，共享状态将包含函数返回的值并`ready`。
		`async`使用的函数原型和参数说明如下：
	```cpp
	//(C++11 起) (C++17 前)
	template< class Function, class... Args>
	std::future<std::result_of_t<std::decay_t<Function>(std::decay_t<Args>...)>>
	    async( Function&& f, Args&&... args );
	
	//(C++11 起) (C++17 前)
	template< class Function, class... Args >
	std::future<std::result_of_t<std::decay_t<Function>(std::decay_t<Args>...)>>
	    async( std::launch policy, Function&& f, Args&&... args );
	```
	- 第一个参数是`std::async`的启动策略类型以下两种方式：`async`允许调用者选择特定的启动策略：
		- `std::launch::async`：在调用`async`就开始创建线程，该函数由新线程异步调用，并且将其返回值与共享状态的访问点同步。
		- `std::launch::deferred`：延迟启动线程，在访问共享状态时该线程才启动。对函数的调用将推迟到返回的`std::future`的共享状态被访问时（即使用`std::future`的`wait`或`get`函数）。如果`get`或`wait`没有被调用，函数就绝对不会执行。
	- 参数`Function`：可以为函数指针、成员指针、任何类型的可移动构造的函数对象，也可以为匿名函数`lambda`。`Function`的返回值或异常存储在共享状态中以供异步的`std::future`对象检索。`std::future`对象可以通过`wait`或`get`函数获取函数的返回值。
	- 参数`Args`：传递给函数`Function`调用的参数，它们的类型应是可移动构造的。
	
	我们可以用多线程将程序分为子任务，用子任务求解，程序示例如下：
	```cpp
	#include <iostream>
	#include <vector>
	#include <algorithm>
	#include <numeric>
	#include <future>
	#include <string>
	#include <mutex>
	  
	template <typename RandomIt>
	int parallel_sum(RandomIt beg, RandomIt end)
	{
	    auto len = end - beg;
	    if (len < 1000)
	        return std::accumulate(beg, end, 0);
	 
	    RandomIt mid = beg + len/2;
	    auto handle = std::async(std::launch::async,
	                             parallel_sum<RandomIt>, mid, end);
	    int sum = parallel_sum(beg, mid);
	    return sum + handle.get();
	}
	
	template <typename RandomIt>
	int parallel_min(RandomIt beg, RandomIt end)
	{
	    auto len = end - beg;
	    if (len < 1000)
	        return *(std::min_element(beg, end));
	 
	    RandomIt mid = beg + len/2;
	    auto handle = std::async(std::launch::async,
	                             parallel_min<RandomIt>, mid, end);
	    int ans = parallel_sum(beg, mid);
	    return std::min(ans, handle.get());
	}
	 
	int main()
	{
	    std::vector<int> v(10000, 1);
	    std::cout << "The sum is " << parallel_sum(v.begin(), v.end()) << '\n';
	    std::cout << "The min element is " << parallel_min(v.begin(), v.end()) << '\n';
	}
	```
3. `std::future`：
	`std::future`提供了一种访问线程异步操作结果的机制。从字面意思来看，`future`表示未来，`std::async`返回结果即为一个`future`。在实际工程项目中，一个异步操作我们是不可能马上就获取操作结果的，只能在将来的某个时候获取，但是我们可以以同步等待的方式来获取结果，可以通过查询`future`的状态（`future_status`）来获取异步操作的结果。`future_status`有三种状态：
	- `deferred`：异步操作还没开始；
	- `ready`：异步操作已经完成；
	- `timeout`：异步操作超时；
	
	获取`future`结果有三种方式：`get`、`wait`、`wait_for`，其中`get`等待异步操作结束并返回结果，`wait`只是等待异步操作完成，没有返回值，`wait_for`是超时等待返回结果。
	```cpp
	#include <iostream>
	#include <future>
	#include <thread>
	 
	int main()
	{
	    // future from an async()
	    std::future<int> f1 = std::async(std::launch::async, []{ return 8; });
	 
	    // future from a promise
	    std::promise<int> p;
	    std::future<int> f2 = p.get_future();
	    std::thread( [&p]{ p.set_value_at_thread_exit(9); }).detach();
	 
	    std::cout << "Waiting..." << std::flush;
	    f1.wait();
	    f2.wait();
	    std::cout << "Done!\nResults are: "
	              << f1.get() << ' ' << f2.get() << '\n';
	}
	/*
	Waiting...Done!
	Results are: 8 9
	*/
	```

[[C++IO与进程同步/readme|返回]]