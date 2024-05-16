

interface类型转换

```go
value    interface{}
op,ok:= value.(要转换的类型)
value.(type)//获取类型
```



# defer

defer 语句会将函数推迟到外层函数返回之后执行。

推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用。


数组
类型 `[n]T` 表示拥有 `n` 个 `T` 类型的值的数组。
```go
var a [10]int

primes := [6]int{2, 3, 5, 7, 11, 13}

var s []int = primes[1:4]
```


range

```go
//当使用 `for` 循环遍历切片时，每次迭代都会返回两个值。第一个值为当前元素的下标，第二个值为该下标所对应元素的一份副本。
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
}
```

映射
`make` 函数会返回给定类型的映射，并将其初始化备用。

```go
	var m map[string]Vertex
	m = make(map[string]Vertex)
	elem, ok := m[key]
	delete(m, key)
	elem = m[key]
```



# 哈希表

Insert or update an element in map `m`:

```
m[key] = elem
```

Retrieve an element:

```
elem = m[key]
```

Delete an element:

```
delete(m, key)
```

Test that a key is present with a two-value assignment:

```
elem, ok = m[key]
```

If `key` is in `m`, `ok` is `true`. If not, `ok` is `false`.

If `key` is not in the map, then `elem` is the zero value for the map's element type.

**Note:** If `elem` or `ok` have not yet been declared you could use a short declaration form:

```
elem, ok := m[key]
```

go panic: assignment to entry in nil map（没有初始化）



# 并发

```go
var wg sync.WaitGroup
wg.add(1)
go func(){
    wg.Done()
}()
wg.Wait()
```





```go
var wg sync.WaitGroup
for i:=0;i<5;i++{
    wg.Add(1)
    go func(x int){
        sendRPC(x)
        wg.Done()
    }(i);
    wg.Wait()
}


var wg sync.WaitGroup
for i:=0;i<5;i++{
    wg.Add(1)
    go func(){
        sendRPC
        ()
        wg.Done()
    }();
    wg.Wait()
}
```

