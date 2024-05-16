# priority_queue(heap)
### push
```c++
  template<typename _RandomAccessIterator, typename _Distance, typename _Tp,
	   typename _Compare>
    void
    __push_heap(_RandomAccessIterator __first,
		_Distance __holeIndex, _Distance __topIndex, _Tp __value,
		_Compare& __comp)
	//首迭代器,插入值索引(__last-1),堆顶索引(0),插入值,比较函数
    {
      _Distance __parent = (__holeIndex - 1) / 2;
      //父节点值与节点值比较，默认less，父节点小，则交换
      while (__holeIndex > __topIndex && __comp(__first + __parent, __value))
	{
	  *(__first + __holeIndex) = _GLIBCXX_MOVE(*(__first + __parent));
	  __holeIndex = __parent;
	  __parent = (__holeIndex - 1) / 2;
	}
      *(__first + __holeIndex) = _GLIBCXX_MOVE(__value);
    }
```




