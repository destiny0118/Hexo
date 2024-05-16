

@resource
postConstruct
# Map


## Map对象遍历

[(50条消息) Java中如何遍历Map对象的4种方法_map遍历_Java高知社区的博客-CSDN博客](https://blog.csdn.net/tjcyjd/article/details/11111401)

```java
//遍历的是一个空的map对象，for-each循环将抛出NullPointerException，因此在遍历前你总是应该检查空引用。
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
 }
//遍历map中的键
for (Integer key : map.keySet()) {
    System.out.println("Key = " + key);
}
 
//遍历map中的值
for (Integer value : map.values()) {
    System.out.println("Value = " + value);
 }
```

## TreeMap
### 自定义排序
```java
package indiv.yyg;

import java.util.Comparator;
import java.util.TreeMap;

/**
 * @description:
 * @author: yyg
 * @date: 2024/5/14
 */
public class Person {
    private  Integer age;

    public Person(Integer age){
        this.age=age;
    }

    public Integer getAge(){
        return age;
    }

    public static void main(String[] args){
        //传入匿名类
        TreeMap<Person,String> treeMap=new TreeMap<>(new Comparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                int num=o1.getAge()-o2.getAge();
                return Integer.compare(num,0);
            }
        });
        treeMap.put(new Person(3), "person1");
        treeMap.put(new Person(18), "person2");
        treeMap.put(new Person(35), "person3");
        treeMap.put(new Person(16), "person4");
        treeMap.entrySet().stream().forEach(personStringEntry -> {
            System.out.println(personStringEntry.getValue());
        });
        //lamada表达式
        TreeMap<Person,String> treeMap1=new TreeMap<>((o1,o2)->{
            int num=o1.getAge()-o2.getAge();
            return Integer.compare(num,0);
        });
    }
}

```

## 时间

[(50条消息) Java中如何获取当前日期和时间的4种方法_获取当前时间_小楼星辰的博客-CSDN博客](https://blog.csdn.net/qq_36789243/article/details/119362598)



## 文件

```java
//获取目录下所有文件名
File dir;
String[] list=dir.list();

//删除目录
public static void deleteDir(File file){
    if(file.isDirectory()){
        for(File f:file.listFiles()){
            deleteDir(f);
        }
    }
    file.delete();
}
```

[Java 实例 – 删除目录 | 菜鸟教程 (runoob.com)](https://www.runoob.com/java/dir-delete.html)

### 复制文件

```java
import java.nio.file.Files;
//from和to是File对象，如果要复制到的文件已经存在，则复制失败
try {
    Files.copy(from.toPath(), to.toPath());
} catch (Exception e) {
}
```

### 删除文件

```java
java.io.File;

File f;
f.delete();
```


```java

    /**
     * 得到当前工作目录下的所有文件
     */
    private void showFileList(File filename) {
        if (filename.isDirectory()) {
            System.out.println("文件夹：" + filename);
            File[] listfile = filename.listFiles();
            for (File f : listfile) {
                showFileList(f);
            }
        } else {
            //    处理
        }
    }
```

# String
```java
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("message: " + message + "\n");
        sb.append("date: " + date + "\n");
        for (String parent : parentID) {
            sb.append(parent + "\n");
        }
        return sb.toString();
    }
```

```java
String str1=null; //引用为空，没有地址，是一个没有被实例化的对象
String str2="";//空字符串，它有地址，它是被实例化的对象，值为空而已。

//如果是string对象是null,用 == 来判断，否则会抛出异常 java.lang.NullPointerException

str1==str2; //“ == ”操作在对String这种引用数据类型来说，比较的是地址
str1.equals(str2) //equals比较的是内容
```