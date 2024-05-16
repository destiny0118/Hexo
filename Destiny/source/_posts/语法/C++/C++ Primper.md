## 左值和右值
[C++中左值和右值的理解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/240833006)
- lvalue(ell-value)：
	- 左值是可寻址的变量，有持久性。既可以出现在等号左边，也可以出现在等号右边
	- 左值表达式的求值结果是一个对象或者一个函数，有些左值不能成为赋值语句的左侧运算对象(const)。当对象被用作左值的时候，用的是object's identity(its location in memory)。
 - rvalue(are-value)：当一个对象被用作右值的时候，用的是对象的值(the object's content)
	 - 右值一般是不可寻址的常量，或在表达式求值过程中创建的无名临时对象，短暂性的
```c++
int a; // a 为左值
a = 3; // 3 为右值
```


需要右值时，可以使用左值，反之不行



- 左值引用：引用一个对象；
- 右值引用（rvalue reference）：就是必须绑定到右值的引用，C++11中右值引用可以实现“移动语义”，通过 && 获得右值引用。必须绑定到右值的引用，可以绑定到一个将要销毁的对象
```cpp
int x = 6; // x是左值，6是右值
int &y = x; // 左值引用，y引用x

int &z1 = x * 6; // 错误，x*6是一个右值
const int &z2 =  x * 6; // 正确，可以将一个const引用绑定到一个右值

int &&z3 = x * 6; // 正确，右值引用
int &&z4 = x; // 错误，x是一个左值
```
# chapt 12

smart pointer：管理动态对象
shared_ptr：允许多个指针指向同一个对象
unique_ptr：独占所指向的对象
weak_ptr：弱引用，指向shared_ptr所管理的对象

管理动态内存：
- 忘记delete动态分配的内存（内存泄漏）
- 在对象delete后使用
- 同一块内存delete多次

## smart pointer

![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202310161042659.png)


![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202310161049131.png)


![](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202310161040197.png)


## unique_ptr
![image.png](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202310161139098.png)
