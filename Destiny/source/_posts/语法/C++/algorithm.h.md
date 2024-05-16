## next_permutation
>next_permutation()是按字典序依次排列的，当排列到最大的值是就会返回false.
>生成给定序列的下一个排列，按字典序生成下一个排列。如果有下一个排列，返回true，否则返回false


```c++
template <typename _BidirectionalIterator, typename _Compare>
bool __next_permutation(_BidirectionalIterator __first,
                        _BidirectionalIterator __last, _Compare __comp) {
    if (__first == __last)  //区间为空
        return false;
    _BidirectionalIterator __i = __first;
    ++__i;
    if (__i == __last)  //区间只有一个元素
        return false;
    __i = __last;
    --__i;

    for (;;) {
        _BidirectionalIterator __ii = __i;  //__i指向__ii前一个位置
        --__i;
        if (__comp(__i, __ii)) {    // *__i<*__ii
            _BidirectionalIterator __j = __last;
            while (!__comp(__i, --__j)) {   //找到从后面起第一个*__j>*__i
            }
            std::iter_swap(__i, __j);
            std::__reverse(__ii, __last, std::__iterator_category(__first));
            return true;
        }
        //区间已经完全逆序
        if (__i == __first) {
            std::__reverse(__first, __last, std::__iterator_category(__first));
            return false;
        }
    }
}
```

## 参考
[使用next_permutation()的坑，你中招了么?_辉小歌的博客-CSDN博客](https://blog.csdn.net/qq_46527915/article/details/115276567)
[C++神奇的next_permutation - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/616792845)