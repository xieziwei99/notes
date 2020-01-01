[TOC]

## Java

### 一些概念知识或常用写法

#### 将 int 数组转为 List-Integer

```java
int[] a = {4, 6, 3, 9, 2};
List<Integer> aList = IntStream.of(a).boxed().collect(Collectors.toList());
```

#### 数组 a 不能直接索引元素，如6，但 List 可以

```java
aList.indexOf(6)	// 返回1
```

#### 将 int[] 转为 Integer[]

```java
int a[] = {1,2,3,4,5};
Integer[] aa = IntStream.of(a).boxed().toArray(Integer[]::new);
Integer[] aa1 = Arrays.stream(a).boxed().toArray(Integer[]::new);
```

#### 初始化 ArrayList

```java
new ArrayList<>(Arrays.asList("tag1", "tag2", "tag3"))
```

#### new Timestamp

```java
// 得到的不是UTC时区
new Timestamp(new Date().getTime())
```

#### 关于 Arrays.asList()

1. **不能将基本类型数组（如 int）作为 asList 的参数**，因为 Arrays.ArrayList 参数为可变长泛型，而基本类型是无法泛型化的，所以它把int[] arr数组当成了一个泛型对象，所以集合中最终只有一个元素arr
2. **将数组作为 asList 参数后，修改数组或 List，会导致出错**，由于 asList 产生的集合元素是直接引用作为参数的数组，所以当外部数组或集合改变时，数组和集合会同步变化，这在平时我们编码时可能产生莫名的问题
3. **将数组转为集合后，不能用 add 和 remove 方法**，因为 asList 产生的集合并没有重写 add, remove 等方法

#### 在 Map 中，根据 value 的最大值找到对应的 key

```java
public static <K, V extends Comparable<V>> Map.Entry<K, V> getMaxEntry(Map<K, V> map) {
        Map.Entry<K, V> maxEntry = null;
        for (Map.Entry<K, V> entry : map.entrySet()) {
            if (maxEntry == null || entry.getValue().compareTo(maxEntry.getValue()) > 0) {
                maxEntry = entry;
            }
        }
        return maxEntry;
    }
```

#### 在 map 中，根据 value 找到对应的一组 keys

```java
public static <K, V extends Comparable<V>> K[] getKeys(Map<K, V> map, V value) {
        List<K> ret = new ArrayList<>();
        for (Map.Entry<K, V> entry : map.entrySet()) {
            if (value.compareTo(entry.getValue()) == 0) {
                ret.add(entry.getKey());
            }
        }
        return (K[])ret.toArray();
    }
```

#### 官方文档对 hashCode 方法的解释

```markdown
1. If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce the same integer result.
翻译：如果两个对象通过equals判断相等，则这两个对象调用hashCode方法的返回值也应该相等。
注：对象调用hashCode方法的返回值就是该对象的哈希值。
2. It is not required that if two objects are unequal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce distinct integer results. However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.
翻译：若两个对象通过equals判断不相等，则这两个对象的hash值可以相等也可以不相等，但不相等可以提升哈希表的性能。
```



### Java 反射机制

#### 类对象

- 概念：所有的类，都存在一个类对象，这个类对象用于提供类本身的信息，比如有几种构造方法， 有多少属性，有哪些普通方法。
- 获取类对象：（假如在 hero 包下有一个 Hero 类）
  1. `Class.forName("hero.Hero");`
  2. `Hero.class;`
  3. `new Hero().getClass();`
- 若 Hero 类有静态初始化块，那么获取类对象时，会执行静态初始化块的代码，且只执行一次，也就是说每个类有且仅有一个类对象。



### HashMap 的方法

#### merge

```java
HashMap<Integer, Integer> ret = new HashMap<>();
int i = 2;
// 有3个参数，param1：键key，param2：新值value，param3：lambda函数
// 如果ret中不存在i，则将新值1关联给i，若存在i，则将旧值与新值计算后关联给i
ret.merge(i, 1, (oldV, newV) -> oldV + newV);

ret.merge(i, 1, Integer::sum);	// 与上面等价
```



### Arrays 方法

#### Arrays.toString()

```java
// 输出数组a
System.out.println(Arrays.toString(a));
```



## springboot

### cloud toolkit 插件，重启 springboot 应用脚本：

```sh
source /etc/profile
killall java

rm /etc/init.d/wemeet

ln -s /home/wemeet/wemeet-0.0.1-SNAPSHOT.jar /etc/init.d/wemeet
chmod 755 /etc/init.d/wemeet
service wemeet start
```


### application.properties 文件

#### 设置 MySQL 数据库

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC
spring.datasource.username=xzw
spring.datasource.password=12345678
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.properties.hibernate.hbm2ddl.auto=update
```



### 参数校验

- 详情参考[这里](https://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247489098&idx=3&sn=ad1883447baa53c663c0da1347ff6514&chksm=9bd0bbd2aca732c49505ecd9256d3b41964413dc092bcd37ddb97a332b2fa9b489ffdd282223&mpshare=1&scene=1&srcid=1227sLd0GB0NDwmzb73pMO4h&sharer_sharetime=1577418505245&sharer_shareid=d8ccde79b2f8a1eb1ce62f41199143e3&key=1d4db1e975a7701b76d425ed43b0f783cb208ef71323d417f481118896228d8459361111508049db90630ef631d6596d16a8a044d7746e94ee7727e4e9aaa42897e67945b5278d46858ea8608dfb4e6c&ascene=1&uin=MzM0MDQzOTAyMg%3D%3D&devicetype=Windows+10&version=62070158&lang=zh_CN&exportkey=A93NSGikgUrL3xVrf3o9fnI%3D&pass_ticket=Pcj%2FjW5IvvyzxOMrYEzY1KUJZ2xzvLaxT6YV51fEUh8Xq7mxvrp8HJ497M0ptReH)

