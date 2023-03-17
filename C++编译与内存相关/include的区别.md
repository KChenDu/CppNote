二者之间的区别：
查找文件的位置：`#include<filename>`通常在编译器或者`IDE`中预先指定的搜索目录中进行搜索，通常会搜索`/usr/include`目录，此方法通常用于包括标准库头文件；`#include "filename"`在当前源文件所在目录中进行查找，如果没有；再到当前已经添加的系统目录（编译时以`-I`指定的目录）中查找，最后会在`/usr/include`目录下查找 。
日常编写程序时，对于标准库中的头文件常用 `#include<filename>`，对于自己定义的头文件常用`#include "filename"`。

[返回](C++编译与内存相关/readme)