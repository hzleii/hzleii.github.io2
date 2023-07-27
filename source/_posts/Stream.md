---
title: Java8 Stream 流操作
date: 2022-06-07
lastmod: 2022-06-07
categories: [Java]
tags: [Stream]
description: Java8 Stream 流操作
---



## 什么是 Stream

`Stream`翻译称为 “流”，是 Java8 的新特性之一。 `Stream`将要处理的元素看作是流，这时可以借助`Stream API`对流中的元素进行中间操作，比如：筛选、排序、排序等。

### 特点

- 不是数据结构，不会保存数据，只会操作数据。
- 不会改变数据源，它会将操作后的数据保存到另外一个对象中。
- 延迟执行，流在中间处理过程中，只是对操作进行了记录，并不会立即执行，需要等到执行**终止操作**的时候才会进行实际的计算。

### 三个步骤

- 创建`Stream`: 从数据源中获取一个流，这个数据源可以是集合、数组。
- 中间操作：操作链，可以对流进行数据处理。
- 终止操作：执行终止操作，才会执行中间操作的操作链，终端操作结束后流无法再次使用，会产生一个新的结果。

## 流的常用创建方法

- 使用`Collection`系列集合提供的`stream()`和`parallelStream()`方法

```java
// 使用Collection系列集合提供的 stream() 和 parallelStream() 方法
ArrayList<String> list = new ArrayList<>();
// 返回一个顺序流
Stream<String> stream = list.stream();
// 把顺序流转换成并行流
Stream<String> parallel = list.stream().parallel();
// 返回一个并行流
Stream<String> stringStream = list.parallelStream();
```

- 使用`Arrays`的静态方法`stream()`可以获取数组流

```java
 String[] strs = new String[10];
 Stream<String> stream = Arrays.stream(strs);
```

- 调用`Stream`类静态方法`of()`

```java
Stream<String> stream = Stream.of("aa", "bbb", "c");
```

- 使用静态方法`Stream.iterate()`和`Stream.generate()`创建无限流

```java
// 迭代
Stream<Integer> iterate = Stream.iterate(0, (x) -> x + 2);
// 0 2 4 6 8
iterate.limit(5).forEach(System.out::println);

// 生成
Stream<Double> generate = Stream.generate(() -> Math.random());
// 84 28 71
generate.limit(3).forEach(System.out::println);
```

可以看到创建`Stream`流的方式有很多。但不论是哪种创建方式，`Stream`不会保存数据，它只会对数据进行计算。

## 流的中间操作

中间操作可以分为两大类：

- 有状态操作：需要等上一步操作完之后，拿到全部元素后才可操作，如 sorted
- 无状态操作：该操作的数据不受上一步操作的影响，如 filter map

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，不然中间操作不会执行任何处理！

### 筛选与切片

| 方法                    | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| **filter(Predicate p)** | 接收 **Lambda**，从流中排除某些元素                          |
| **distinct()**          | 筛选，通过流所产生的 **hashCode()** 和 **equals()** 去除重复元素 |
| **limit(long maxSize)** | 截断流，使其元素不超过给定数量                               |
| **skip(long n)**        | 跳过元素，返回一个扔掉了前 n 个元素的流，若流中元素不足 n 个，则返回一个空流，与 **limit(n)** 互补 |

```java
// 流的中间操作
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 2, 5, 7, 9, 17);
/**
 * 1.筛选与切片
 * filter：过滤流中的某些元素
 * limit(n)：获取 n 个元素
 * skip(n)：跳过 n 元素，若流中元素不足 n 个，则返回一个空流，配合 limit(n) 可实现分页
 * distinct：通过流中元素的 hashCode() 和 equals() 去除重复元素
 */
// 创建流
Stream<Integer> stream = integers.stream();
// 中间操作
Stream<Integer> integersStream = stream
        .filter((t) -> t > 5)   // 6 7 8 9 10 7 9 17
        .distinct()  // 6 7 8 9 10 17
        .skip(2)   // 8 9 10 17
        .limit(3);  //8 9 10
integersStream.forEach(System.out::println);
```

### 映射

| 方法                                | 描述                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| **map(Function f)**                 | 接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素 |
| **mapToDouble(ToDoubleFunction f)** | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的    DoubleStream |
| **mapToInt(ToIntFunction f)**       | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的    IntStream |
| **mapToLong(ToLongFunction f)**     | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的    LongStream |
| **flatMap(Function f)**             | 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连成一个流 |

- map

```java
// 将每个元素转换为大写
List<String> list = Arrays.asList("a,a=", "b,b=", "cc=", "d,d=");
List<String> collect = list.stream().map(String::toUpperCase).collect(Collectors.toList());
// str -> str.toUpperCase() 可以简写为 String::toUpperCase
System.out.println(collect);
// 结果：[A,A=, B,B=, CC=, D,D=]
```

```java
List<Employee> employees = Arrays.asList(
        new Employee("张三", 23, 9999.99),
        new Employee("李四", 24, 5555.55),
        new Employee("王五", 25, 6666.66),
        new Employee("赵六", 26, 3333.33),
        new Employee("周七", 27, 7777.77)
);

// 提取员工名称
List<String> collect1 = employees.stream()
        // 类::实例方法名
        .map(Employee::getName)
        .collect(Collectors.toList());
System.out.println(collect1);

// 输出：[张三, 李四, 王五, 赵六, 周七]
```


- mapToInt
```java
List<String> list = Arrays.asList("a,a", "b,b", "cc", "d,d");
IntStream intStream = list.stream()
        .mapToInt(String::length);

intStream.forEach(System.out::println);
// 输出：
// 3
// 3
// 2
// 3
```

- flatMap

```java
List<String> list = Arrays.asList("a,a=","b,b=","cc=","d,d=");
Stream<String> stringStream = list.stream()
        .flatMap(str -> {
            String[] split = str.split(",");
            Stream<String> stream = Arrays.stream(split);
            return stream;
        });
List<String> collect = stringStream.collect(Collectors.toList());
System.out.println(collect);
// 输出：
// [a, a=, b, b=, cc=, d, d=]
```

### 排序

| 方法                       | 描述                                 |
| -------------------------- | ------------------------------------ |
| **sorted()**               | 产生一个新的流，其中按自然顺序排序   |
| **sorted(Comparator com)** | 产生一个新的流，其中按比较器顺序排序 |

- sorted

```java
List<String> list = Arrays.asList("bb", "cc", "ee", "aa");
list.stream().sorted().forEach(System.out::println);
// 输出：
// aa
// bb
// cc
// dd
```

- sorted(Comparator com)

```java
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);
// 如果年龄一致，就按照姓名排序，如果不一致，就按照年龄升序排序
employees.stream().sorted((e1, e2) -> {
    if (e1.getAge().equals(e2.getAge())) {
        return e1.getName().compareTo(e2.getName());
    } else {
        return e1.getAge().compareTo(e2.getAge());
    }
}).forEach(System.out::println);
// 输出：
// name='赵六', age=15, salary=3333.33
// name='张三', age=18, salary=9999.99
// name='周七', age=18, salary=7777.77
// name='李四', age=38, salary=5555.55
// name='王五', age=50, salary=6666.66
```

## 流的终止操作

- 终端操作会从流水线中产生结果。其结果可以是任何不是流的值，例如：List、Integer、String

- 流进行了终止操作后，不能再次使用。

### 匹配和查找

| 方法                       | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| **allMatch(Predicate p)**  | 检查是否匹配所有元素                                         |
| **anyMatch(Predicate p)**  | 检查是否至少匹配一个元素                                     |
| **noneMatch(Predicate p)** | 检查是否没有匹配所有元素                                     |
| **findFirst()**            | 返回第一个元素                                               |
| **findAny()**              | 返回当前流中的任意元素                                       |
| **count()**                | 返回流中元素总数                                             |
| **max(Comparator c)**      | 返回流中最大值                                               |
| **min(Comparator c)**      | 返回流中最小值                                               |
| **forEach(Consumer c)**    | 内部迭代(使用 `Collection` 接口需要用户去做迭代，称为外部迭代)。 |

- allMatch

```java
// 判断数据流中所有元素是否与 aa 相等，全部相等则返回true
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
boolean aa = list.stream().allMatch(s -> s.equals("aa"));
System.out.println(aa);
// 输出：
// false

List<String> list = Arrays.asList("aa", "aa", "aa", "aa");
boolean aa = list.stream().allMatch(s -> s.equals("aa"));
System.out.println(aa);
// 输出：
// true
```

- anyMatch

```java
// 判断数据流中所有元素是否有一个与 aa 相等，相等则返回 true
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
boolean aa = list.stream().anyMatch(obj -> obj.equals("aa"));
System.out.println(aa);
// 输出：
// true
```

- noneMatch

```java
// 判断数据流中所有元素是否全部与 aa1 相等，一个都没有匹配成功则返回 true。
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
boolean aa = list.stream().noneMatch(obj -> obj.equals("aa1"));
System.out.println(aa);
// 输出：
// true
```

- findFirst

```java
// 返回第一个元素
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
Optional<String> first = list.stream().findFirst();
System.out.println(first.get());
// 输出：
// aa
```

- findAny

```java
// 返回当前流中的任意元素
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
Optional<String> first = list.stream().findAny();
System.out.println(first.get());
// 输出：
// aa

// 由于是顺序流所以每次返回的都是第一个元素。使用并行流：
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
Optional<String> first = list.parallelStream().findAny();
System.out.println(first.get());
```

- count

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
long count = list.stream().count();
System.out.println(count);
// 输出：
// 5
```

- max(Comparator c)

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> max = list.stream().max(Integer::compareTo);
System.out.println(max.get());
// 输出：
// 5
```

- min(Comparator c)

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> max = list.stream().min(Integer::compareTo);
System.out.println(max.get());
// 输出：
// 1
```

- forEach(Consumer c)

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
list.forEach(System.out::println);
// 输出：
// 1
// 2
// 3
// 4
// 5
```

## 规范

| 方法                                          | 描述                                                      |
| :-------------------------------------------- | --------------------------------------------------------- |
| **T reduce(T i, BinaryOperator\<T> a)**       | 可以将流中元素反复结合起来，得到一个值。返回 T            |
| **Optional\<T> reduce(BinaryOperator\<T> a)** | 可以将流中元素反复结合起来，得到一个值，返回`Optional<T>` |

- T reduce(T i, BinaryOperator\<T> a)

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Integer reduce = list.stream().reduce(0, (x1, x2) -> x1 + x2);
System.out.println(reduce);
// 输出：
// 55

// 第一个参数是起始值，如果起始值为10呢？
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Integer reduce = list.stream().reduce(10, (x1, x2) -> x1 + x2);
System.out.println(reduce);
// 输出：
// 65
```

第一次执行时，会将 `i`(也就是 10 ) 的值作为 `a` 函数的第一个参数，第二个参数为流中元素的第一个元素；第二次执行时，第一个参数为第一次函数执行的结果，第二个参数为流中的第二个元素；依次类推。

- Optional\<T> reduce(BinaryOperator\<T> a)

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Optional<Integer> reduce = list.stream().reduce(Integer::sum);
System.out.println(reduce.get());
// 输出：
// 55
```

流程与第一个方法是一致的，只是第一次执行时，第一个参数为流中的第一个元素，第二个参数为流中元素的第二个元素。

## 收集

| 方法                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| **collect(Collector c)** | 将流转换为其他形式。接收一个`Collector`接口的实现，用于给`Stream`中元素做汇总的方法 |

`Collector` 接口中方法的实现决定了如何对流执行收集的操作(如收集到 List、Set、Map中)。 另外， `Collectors` 类提供了很多静态方法，可以方便地创建常见收集器实例，具体方法与实例如下表：

| 方法                  | 返回类型                | 作用                                                         |
| --------------------- | ----------------------- | ------------------------------------------------------------ |
| **toList**            | List\<T>                | 把流中元素收集到`List`                                       |
| **toSet**             | Set\<T>                 | 把流中元素收集到`Set`                                        |
| **toCollection**      | Collection\<T>          | 把流中元素收集到创建的集合                                   |
| **counting**          | Long                    | 计算流中元素的个数                                           |
| **summingInt**        | Integer                 | 对流中元素的整数属性求和                                     |
| **averagingInt**      | Double                  | 计算流中元素`Integer`属性的平均值                            |
| **summarizingInt**    | IntSummaryStatistics    | 收集流中`Integer`属性的统计值，如：平均值                    |
| **joining**           | String                  | 连接流中每个字符串                                           |
| **maxBy**             | Optional\<T>            | 根据比较器选择最大值                                         |
| **minBy**             | Optional\<T>            | 根据比较器选择最小值                                         |
| **reducing**          | 归约产生的类型          | 从一个作为累加器的初始值开始，利用`BinaryOperator`与流中元素逐个结合，从而归约成单个值 |
| **collectingAndThen** | 转换函数返回的类型      | 包裹另一个收集器，对其结果转换函数                           |
| **groupingBy**        | Map\<K, List\<T>>       | 根据某属性对流进行分组，属性为K，结果为V                     |
| **partitioningBy**    | Map\<Boolean, List\<T>> | 根据`true`或`false`进行分区                                  |

- toList

```java
// 获取到员工的姓名并放入 List 集合中
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);
List<String> collect = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.toList());
collect.forEach(System.out::println);
// 输出：
// 张三
// 李四
// 王五
// 赵六
// 周七
```

- toCollection

```java
// 获取到员工的姓名并放入 HashSet 集合中
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);
HashSet<String> hs = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.toCollection(HashSet::new));
hs.forEach(System.out::println);
```

- counting、summingDouble、averagingDouble、maxBy

```java
// 分别为：计数、求和、平均值、最值
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);
// 元素的个数
Long l = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.counting());
System.out.println("个数：" + l);
// 求和
Double sum = employees.stream()
        .collect(Collectors.summingDouble(Employee::getAge));
System.out.println("求和：" + sum);
// 平均值
Double avg = employees.stream()
        .collect(Collectors.averagingDouble(Employee::getAge));
System.out.println("平均值：" + avg);
// 获得年龄最大的员工信息
Optional<Employee> collect = employees.stream()
        .collect(Collectors.maxBy((e1, e2) -> Integer.compare(e1.getAge(), e2.getAge())));
System.out.println("获得年龄最大的员工信息：" + collect.get());

// 输出：
// 个数：5
// 求和：139.0
// 平均值：27.8
// 获得年龄最大的员工信息：name='王五', age=50, salary=6666.66
```

- summarizingInt

```java
// 收集流中 Integer 属性的统计值。
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);
IntSummaryStatistics collect = employees.stream().collect(Collectors.summarizingInt(Employee::getAge));
System.out.println("个数：" + collect.getCount());
System.out.println("求和：" + collect.getSum());
System.out.println("最大值：" + collect.getMax());
System.out.println("平均值：" + collect.getAverage());
System.out.println("最小值：" + collect.getMin());

// 输出：
// 个数：5
// 求和：139
// 最大值：50
// 平均值：27.8
// 最小值：15
```

- groupingBy

```java
// 根据年龄进行分组
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);

Map<Integer, List<Employee>> collect = employees.stream().collect(Collectors.groupingBy(Employee::getAge));

for(Integer key : collect.keySet()){
    System.out.println("key: " + key + ", value: " + collect.get(key));
}

// 输出：
// key: 50, value: [Employee(name=王五, age=50, salary=6666.66)]
// key: 18, value: [Employee(name=张三, age=18, salary=9999.99), Employee(name=周七, age=18, salary=7777.77)]
// key: 38, value: [Employee(name=李四, age=38, salary=5555.55)]
// key: 15, value: [Employee(name=赵六, age=15, salary=3333.33)]
```

- partitioningBy：将 stream 按条件分为两个 Map，true或false

```java
// 薪资是否大于7000
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);

Map<Boolean, List<Employee>> collect = employees.stream().collect(Collectors.partitioningBy(e -> e.getSalary() > 7000));

for(Boolean key : collect.keySet()){
    System.out.println("key: " + key + ", value: " + collect.get(key));
}

// 输出：
// key: false, value: [Employee(name=李四, age=38, salary=5555.55), Employee(name=王五, age=50, salary=6666.66), Employee(name=赵六, age=15, salary=3333.33)]
// key: true, value: [Employee(name=张三, age=18, salary=9999.99), Employee(name=周七, age=18, salary=7777.77)]
```

- joining

```java
// 连接流中每个字符串
List<Employee> employees = Arrays.asList(
        new Employee("张三", 18, 9999.99),
        new Employee("李四", 38, 5555.55),
        new Employee("王五", 50, 6666.66),
        new Employee("赵六", 15, 3333.33),
        new Employee("周七", 18, 7777.77)
);

String collect = employees.stream()
        .map(Employee::getName)
        .collect(Collectors.joining(", ","这些人：","他们好棒"));
System.out.println("str: " + collect);

// 输出：
// str: 这些人：张三, 李四, 王五, 赵六, 周七他们好棒
```

