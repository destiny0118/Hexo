# set

## multiset

[(41条消息) multiset用法总结_二喵君的博客-CSDN博客](https://blog.csdn.net/sodacoco/article/details/84798621)

## 常用函数总结：

**构造、拷贝、析构**

<table><tbody><tr><td><p><strong>操作</strong></p></td><td><p><strong>效果</strong></p></td></tr><tr><td><p><em>set</em>&nbsp;c</p></td><td><p>产生一个空的set/multiset，不含任何元素</p></td></tr><tr><td><p>set c(op)</p></td><td><p>以op为排序准则，产生一个空的set/multiset</p></td></tr><tr><td><p>set c1(c2)</p></td><td><p>产生某个set/multiset的副本，所有元素都被拷贝</p></td></tr><tr><td><p>set c(beg,end)</p></td><td><p>以区间[beg,end)内的所有元素产生一个set/multiset</p></td></tr><tr><td><p>set c(beg,end, op)</p></td><td><p>以op为排序准则，区间[beg,end)内的元素产生一个set/multiset</p></td></tr><tr><td><p>c.~set()</p></td><td><p>销毁所有元素，释放内存</p></td></tr><tr><td><p>set&lt;Elem&gt;</p></td><td><p>产生一个set，以(operator &lt;)为排序准则</p></td></tr><tr><td><p>set&lt;Elem,0p&gt;</p></td><td><p>产生一个set，以op为排序准则</p></td></tr></tbody></table>

### **非变动性操作**

<table><tbody><tr><td><p><strong>操作</strong></p></td><td><p><strong>效果</strong></p></td></tr><tr><td><p>c.size()</p></td><td><p>返回当前的元素数量</p></td></tr><tr><td><p>c.empty ()</p></td><td><p>判断大小是否为零，等同于0 == size()，效率更高</p></td></tr><tr><td><p>c.max_size()</p></td><td><p>返回能容纳的元素最大数量</p></td></tr><tr><td><p>c1 == c2</p></td><td><p>判断c1是否等于c2</p></td></tr><tr><td><p>c1 != c2</p></td><td><p>判断c1是否不等于c2(等同于!(c1==c2))</p></td></tr><tr><td><p>c1 &lt; c2</p></td><td><p>判断c1是否小于c2</p></td></tr><tr><td><p>c1 &gt; c2</p></td><td><p>判断c1是否大于c2</p></td></tr><tr><td><p>c1 &lt;= c2</p></td><td><p>判断c1是否小于等于c2(等同于!(c2&lt;c1))</p></td></tr><tr><td><p>c1 &gt;= c2</p></td><td><p>判断c1是否大于等于c2 (等同于!(c1&lt;c2))</p></td></tr></tbody></table>

### **特殊的搜寻函数**

　　sets和multisets在元素快速搜寻方面做了优化设计，提供了特殊的搜寻函数，所以应优先使用这些搜寻函数，可获得对数复杂度，而非STL的线性复杂度。比如在1000个元素搜寻，对数复杂度平均十次，而线性复杂度平均需要500次。

<table><tbody><tr><td><p><strong>操作</strong></p></td><td><p><strong>效果</strong></p></td></tr><tr><td><p>count (elem)</p></td><td><p>返回元素值为elem的个数</p></td></tr><tr><td><p>find(elem)</p></td><td><p>返回元素值为elem的第一个元素，如果没有返回end()</p></td></tr><tr><td><p>lower _bound(elem)</p></td><td><p>返回元素值为elem的第一个可安插位置，也就是元素值 &gt;= elem的第一个元素位置</p></td></tr><tr><td><p>upper _bound (elem)</p></td><td><p>返回元素值为elem的最后一个可安插位置，也就是元素值 &gt; elem 的第一个元素位置</p></td></tr><tr><td><p>equal_range (elem)</p></td><td><p>返回elem可安插的第一个位置和最后一个位置，也就是元素值==elem的区间</p></td></tr></tbody></table>

### **赋值**

<table><tbody><tr><td><p><strong>操作</strong></p></td><td><p><strong>效果</strong></p></td></tr><tr><td><p>c1 = c2</p></td><td><p>将c2的元素全部给c1</p></td></tr><tr><td><p>c1.swap(c2)</p></td><td><p>将c1和c2 的元素互换</p></td></tr><tr><td><p>swap(c1,c2)</p></td><td><p>同上，全局函数</p></td></tr></tbody></table>

### **迭代器相关函数**

　　sets和multisets的迭代器是双向迭代器，对迭代器操作而言，所有的元素都被视为常数，可以确保你不会人为改变元素值，从而打乱既定顺序，所以无法调用变动性算法，如remove()。

<table><tbody><tr><td><p><strong>操作</strong></p></td><td><p><strong>效果</strong></p></td></tr><tr><td><p>c.begin()</p></td><td><p>返回一个随机存取迭代器，指向第一个元素</p></td></tr><tr><td><p>c.end()</p></td><td><p>返回一个随机存取迭代器，指向最后一个元素的下一个位置</p></td></tr><tr><td><p>c.rbegin()</p></td><td><p>返回一个逆向迭代器，指向逆向迭代的第一个元素</p></td></tr><tr><td><p>c.rend()</p></td><td><p>返回一个逆向迭代器，指向逆向迭代的最后一个元素的下一个位置</p></td></tr></tbody></table>

### 安插和删除元素

　　必须保证参数有效,迭代器必须指向有效位置，序列起点不能位于终点之后，不能从空容器删除元素。

<table><tbody><tr><td><p><strong>操作</strong></p></td><td><p><strong>效果</strong></p></td></tr><tr><td><p>c.insert(elem)</p></td><td><p>插入一个elem副本，返回新元素位置，无论插入成功与否。</p></td></tr><tr><td><p>c.insert(pos, elem)</p></td><td><p>安插一个elem元素副本，返回新元素位置，pos为收索起点，提升插入速度。</p></td></tr><tr><td><p>c.insert(beg,end)</p></td><td><p>将区间[beg,end)所有的元素安插到c，无返回值。</p></td></tr><tr><td><p>c.erase(elem)</p></td><td><p>删除与elem相等的所有元素，返回被移除的元素个数。</p></td></tr><tr><td><p>c.erase(pos)</p></td><td><p>移除迭代器pos所指位置元素，无返回值。</p></td></tr><tr><td><p>c.erase(beg,end)</p></td><td><p>移除区间[beg,end)所有元素，无返回值。</p></td></tr><tr><td><p>c.clear()</p></td><td><p>移除所有元素，将容器清空</p></td></tr></tbody></table>

