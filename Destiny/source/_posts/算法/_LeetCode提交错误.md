runtime error: reference binding to misaligned address 0xbebebebebebec0ba for type 'int', which requires 4 byte alignment (stl_deque.h)
当栈为空，访问栈顶元素

runtime error: reference binding to null pointer of type 'long long' (stl_vector.h)
vector为空，未初始化 



AddressSanitizer: heap-use-after-free on address

从堆中弹出元素时使用了引用
```c++
 typedef  pair<int,int> pii;
 priority_queue<pii,vector<pii>,greater<>> pq;
        pq.push({0,0});
        while(!pq.empty()){
            auto &[d,u]=pq.top();
}
```