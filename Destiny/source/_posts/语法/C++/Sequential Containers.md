# Sequential Containers

元素在顺序容器中的顺序与其加入容器时的位置相对应。

## Container Operations

![image-20230524163541746](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202305241635831.png)

```c++
vector<int> vec;
vector<int>::iterator iter1 = vec.begin(), iter2 = vec.end();
[begin,end)
```





定义和初始化容器

| C c             | 默认初始化          |
| --------------- | ------------------- |
| C c1(c2)        |                     |
| C c1=c2         |                     |
| C c{a,b,c,...}  |                     |
| C c={a,b,c,...} |                     |
| C c(b,e)        | 迭代器初始化，[b,e) |
| C seq(n)        |                     |
| C seq(n,t)      | n个元素，值为t      |











## Container Adaptors

> 容器适配器

![image-20230523175546695](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202305231755730.png)

### stack

![image-20230523175309310](https://raw.githubusercontent.com/destiny0118/picgo/master/pic2023/202305231753373.png)

### queue







### priority_queue

```
template <class T, class Container = vector<T>,  class Compare = less<typename Container::value_type> > class priority_queue;
```

> Compare: 
>
> comp(a,b)，如果a应该出现在b前面，返回true，弹出的元素是根据*strict weak ordering*的最后一个元素
>
> A binary predicate that takes two elements (of type T) as arguments and returns a `bool`.
> The expression `comp(a,b)`, where comp is an object of this type and a and b are elements in the container, shall return `true` if a is considered to go before b in the *strict weak ordering* the function defines.
> The priority_queue uses this function to maintain the elements sorted in a way that preserves *heap properties* (i.e., that the element popped is the last according to this *strict weak ordering*).
> This can be a function pointer or a function object, and defaults to `less<T>`, which returns the same as applying the *less-than operator* (`a<b`).