---
title: Java8新特性
date: 2021-06-20 00:00:00
author: 神奇的荣荣
summary: ""
categories: Java
tags: 
    - java
    - jdk
---

# Java8新特性简介

1）速度更快  
2）代码更少（增加了新的语法Lambda表达式）  
3）强大的Stream API  
4）便于并行  
5）最大化减少空指针异常 Optional

**其中 Lambda 表达式与 Stream API 最为核心的。**

<!-- more -->

# Lambda表达式

## 什么是Lambda 表达式

Lambda 表达式，也可称为闭包，一个匿名函数，它是推动 Java 8 发布的最重要新特性。  
Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）。  
使用 Lambda 表达式可以使代码变的更加简洁紧凑。

## Lambda 表达式

Lambda 表达式的基础语法：  
Java8中引入了一个新操作符：“->”该操作符被称为剪头操作符或 Lambda 操作符。

箭头操作符将 Lambda 表达式拆分成两部分：  
左侧：指定了 Lambda 表达式需要的所有参数。  
右侧：指定了 Lambda 体，即 Lambda 表达式所需执行的功能。  
列如：(parameters) -> expression 或 (parameters) ->{ statements; }

特征：  
可选类型声明：不需要声明参数类型，编译器可以统一识别参数值。  
可选的参数圆括号：一个参数无需定义圆括号，但多个参数需要定义圆括号。  
可选的大括号：如果主体包含了一个语句，就不需要使用大括号。  
可选的返回关键字：如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。

上联：左右遇一括号省  
下联：左侧推断类型省  
横批：能省则省

## 语法

1）语法格式一：无参，无返回值
```java
Runnable runnable = () -> System.out.println("hello lambda!!!");
```

2）语法格式二：有一个参数，并且无返回值
```java
Consumer<String> consumer = (x) -> System.out.println(x);
```

3）语法格式三：若只有一个参数，小括号可以省略不写
```java
Consumer<String> consumer = x -> System.out.println(x);
```

4）语法格式四：有两个以上参数，有返回值，并且Lambda体中有多条语句
```java
Comparator<Integer> comparator = (x, y) -> {
    System.out.println("开始比较");
    return Integer.compare(x, y);
};
```

5）语法格式五：若Lambda体中只有一条语句，return和大括号都可不写
```java
Comparator<Integer> comparator = (x, y) -> Integer.compare(x, y);
```

（6）语法格式六：Lambda 表达式的参数列表的数据类型可以省略不写，因为jvm通过上下文推断出，数据类型，即‘类型推断’
```java
Comparator<Integer> comparator = (Integer x, Integer y) -> Integer.compare(x, y);
```

## Lambda表达式需要“函数式接口”的支持

详情看：函数式接口

# 函数式接口

## 什么是函数式接口？

函数式接口：  
接口中只有一个抽象方法的接口，称为函数式接口。  
可以使用注解 @FunctionalInterface 修饰。

```java
@FunctionalInterface    // 表示这是函数式接口
public interface MyFun {

    String getValue(String str);

    String test();多一个方法就报错

}
```

## Java8 内置的四大核心函数式接口

1）Consumer<T>：消费型接口  
Void accept(T t)

2）Supplier<T>：供给型接口  
T get();

3）Function<T, R>：函数型接口  
R apply(T t);

4）Predicate<T>：断言型接口  
Boolean test(T t);

## 其他接口

![其他函数式接口](https://rong0624.github.io/images/Java/JDK/其他函数式接口.png)

# 方法引用与构造函数引用

## 方法引用

方法引用：  
若 Lambda 体中的内容有方法已经实现了，我们可以使用“方法引用”  
（可以理解为方法引用是Lambda表达式的另外一种表现方式）  
方法引用可以使语言的构造更紧凑简洁，减少冗余代码。

### 语法

方法引用三种语法格式：  
```
第一种：实例对象::实例方法名  

第二种：类::静态方法名  

第三种：类::实例方法名
```

注意：  
（1）Lambda 体中调用方法的参数列表与返回值类型，要与函数式接口中抽象方法的参数列表和返回值保持一致。  
（2）若Lambda 参数列表中的第一个参数是实例方法的调用者，而第二个参数是实例方法的参数时，可以使用类::实例方法名。

### 案例

```java
// 方法引用：对象::方法名
public void test1() {
    Consumer<String> con1 = x -> System.out.println(x);
    Consumer<String> con2 = System.out::print;

    User user = new User();
    Supplier<String> sup1 = () -> user.getName();
    Supplier<String> sup2 = user::getName;
}

// 方法引用：类名::静态方法名
public void test2() {
    Comparator<Integer> com1 = (x, y) -> Integer.compare(x, y);
    Comparator<Integer> com2 = Integer::compare;
}

// 方法引用：类名：实例方法名
public void test3() {
    BiPredicate<String, String> bp1 = (x, y) -> x.equals(y);
    BiPredicate<String, String> bp2 = String::equals;
}
```

## 构造器引用

### 语法

语法格式：Class :: method

注意：需要调用的构造器的参数列表与返回值，要与函数式接口中的抽象方法的参数列表与返回值保持一致。

### 案例

```java
// 构造器引用：类名::new
public void test4() {
    // 对应函数式接口的抽象方法，当前调用无参构造
    Supplier<User> su1 = () -> new User();
    Supplier<User> su2 = User::new;

    // 对应函数式接口的抽象方法，当前调用参数为integer类型的构造器
    Function<Integer, User> fun1 = User::new;

    // 对应函数式接口的抽象方法，当前调用参数为string,integer的构造器
    BiFunction<String, Integer, User> fun2 = User::new;
}
```

# Stream API

## 了解Stream API

Java8中有两大最为重要的改变：   
第一个是Lambda表达式；  
第二个则是Stream API（java.util.stream.*）；  

Stream API是Java8中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找，过滤，映射数据等操作。  
使用Stream API对集合数据进行操作，就类似使用SQL执行的数据库查询。也可以使用Stream API来执行并行操作。简而言之，Stream API提供了一种高效且易于使用的处理数据的方式。

## 什么是Stream

流（Stream）到底是什么呢？  
是数据渠道，用于操作数据源（集合，数组等）所生成的元素序列。  
“集合讲的是数据，流讲的是计算！！！”

注意：  
1）Stream 自己不会存储元素  
2）Stream 不会改变源对象。相反，它会返回一个持有结果的新Stream  
3）Stream 操作是延迟执行的。这意味着它会等到需要结果的时候才执行（懒加载概念）

## Stream的三个操作步骤

1）创建Stream  
一个数据源（如：集合，数组），获取一个流

2）中间操作  
一个中间操作链，对数据源的数据进行处理（过滤，映射）

3）终止操作（终端操作，返回结果）  
一个终止操作，执行中间操作链，并且产生结果

流程图：  
![Stream操作流程图](https://rong0624.github.io/images/Java/JDK/Stream操作流程图.png)

## 创建Stream

### 集合创建Stream

Java8 中的 Collection 接口被扩展，提供了两个获取流的方法：  
default Stream<E> stream() : 返回一个顺序流  
default Stream<E> parallelStream() : 返回一个并行流

案例：
```java
@Test
public void test1() {
    // List Set 继承至 Collection
    all.stream().forEach(System.out::println);
    all.parallelStream().forEach(System.out::println);
    map.values().stream().forEach(System.out::println);
}
```

### 数组创建Stream

Java8 中的 Arrays 的静态方法 stream() 可以获取数组流：  
static <T> Stream<T> stream(T[] array): 返回一个流。

重载形式，能够处理对应基本类型的数组： 
public static IntStream stream(int[] array) 
public static LongStream stream(long[] array)
public static DoubleStream stream(double[] array)

案例：
```java
@Test
public void test2() {
    int[] intArr = {1, 2, 3, 4};
    String[] strArr = {"a", "b", "c", "d"};
    Arrays.stream(intArr).forEach(System.out::println);
    Arrays.stream(strArr).forEach(System.out::println);
}
```

### 值创建Stream

可以使用静态方法 Stream.of(), 通过显示值创建一个流，它可以接收任意数量的参数：  
public static<T> Stream<T> of(T... values) : 返回一个流

案例：
```java
public void test3() {
    Stream.of("a", "b", "c", "d").forEach(System.out::println);
    Stream.of("a", "b", "c", "d").parallel().forEach(System.out::println);
    Stream.of(new User(50, "张三"),
            new User(60, "李四"),
            new User(11, "王五")).forEach(System.out::println);
}
```

### 函数创建Stream（无限流）

可以使用静态方法 Stream.iterate() 和 Stream.generate(), 创建无限流。 
迭代：  
public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f);

生成：  
public static<T> Stream<T> generate(Supplier<T> s);

## Stream的中间操作

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止操作，否则中间操作不会执行任何的处理!  
而在终止操作时一次性全部处理，称为“惰性求值”（类似mybtais的懒加载，redis的惰性删除，只有最后使用时才会真正执行）。

### 筛选与切片

filter(Predicate p)：接收Lambda，从Stream流中排除某些元素。

distinct()：筛选，通过Stream流所生成元素的 hashCode() 和 equals() 去除重复元素。

limit(long maxSize)：截断Stream流，使其返回元素不超过指定数量。

skip(long n)：跳过元素，返回扔掉前n个元素的流，若流中元素不足n个，则返回一个空流。与limit(n) 互补。

案例：
```java
@Test
public void test1() {
    all.stream().filter(user -> user.getAge() > 10).forEach(System.out::println);
    all.stream().distinct().forEach(System.out::println);
    all.stream().limit(10).forEach(System.out::println);
    all.stream().skip(10).forEach(System.out::println);
    all.stream().distinct()
            .filter(user -> user.getAge() > 10)
            .limit(10)
            .skip(5)
            .forEach(System.out::println);
}
```

### 映射

map(Function mapper)：接受一个函数（lambda）作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。

flatMap(Function mapper)：接受一个函数（lambda）作为参数，将流中每个元素都转成一个流，然后把全部流连成一个流返回。

```java
public void test2() {
    all.stream().map(user -> user.getName())
                .forEach(System.out::println);
    all.stream().flatMap(user -> Stream.of(user.getName()))
            .forEach(System.out::println);
}
```

### 排序

sorted()：自然排序，使用Comparable默认排序。

sorted(Comparator comparator)：定制排序，使用Comparator。

```java
public void test3() {
    List<String> strings = Arrays.asList("ccc", "aaa", "ddd", "zzz");
    strings.stream()
            .sorted()
            .forEach(System.out::println);

    all.stream().sorted((x, y) -> {
        if (x.getAge().equals(y.getAge())) {
            return x.getName().compareTo(y.getName());
        }
        return x.getAge().compareTo(y.getAge());
    }).forEach(System.out::println);
}
```

## Stream的终止操作

### 查找与匹配

Find查找：  
findFirst() ：返回第一个元素   
findAny() ：返回当前流中的任意元素  
count() ：返回流中元素总个数  
max(Comparator c) ：返回流中最大值  
min(Comparator c) ：返回流中最小值  

Match匹配：  
allMatch(Predicate p) ：检查是否匹配所有元素   
anyMatch(Predicate p) ：检查是否至少匹配一个元素   
noneMatch(Predicate p) ：检查是否没有匹配所有元素

Final 查找案例：
```java
@Test
public void test2() {
    Optional<User> firstOptional = all.stream().findFirst();
    Optional<User> anyOptional = all.stream().findAny();
    long count = all.stream().count();
    Optional<User> maxOptional = all.stream().max(Comparator.comparing(User::getAge));
    Optional<Integer> minOptional = all.stream().map(User::getAge).min(Integer::compareTo);
    System.out.println(firstOptional.get());
    System.out.println(anyOptional.get());
    System.out.println(count);
    System.out.println(maxOptional.get());
    System.out.println(minOptional.get());
}
```

Match 匹配Demo：
```java
@Test
public void test3() {
    boolean allMatch = all.stream().allMatch(x -> UserStatus.A == x.getStatus());
    boolean anyMatch = all.stream().anyMatch(x -> UserStatus.B == (x.getStatus()));
    boolean noneMatch = all.stream().noneMatch(x -> UserStatus.A == x.getStatus());
    System.out.println(allMatch);
    System.out.println(anyMatch);
    System.out.println(noneMatch);
}
```

### 规约

reduce(T iden, BinaryOperator b) ： 可以将流中元素反复结合起来，得到一个值。返回T。

reduce(BinaryOperator b) ： 可以将流中元素反复结合起来，得到一个值，返回Optional<T>。

```java
@Test
public void reduceTest() {
    BigDecimal reduce = all.stream().map(User::getAmount).reduce(BigDecimal.ZERO, (o, n) -> {
        o = Optional.ofNullable(o).orElse(BigDecimal.ZERO);
        n = Optional.ofNullable(n).orElse(BigDecimal.ZERO);
        return o.add(n);
    });
    System.out.println(reduce);
}
```

### 收集

collect(Collector c) ：将流转换为其他形式，接受一个Collector接口的实现，用于给Stream中元素做汇总操作。

Collector 接口中方法的实现决定了如何对流执行收集操作（如：收集到List，Set，Map）。但是Collectors 实现类提供了很多静态方法，可以方便的创建常见收集器实例，具体方法与实例如下表：  
![常见收集器方法](https://rong0624.github.io/images/Java/JDK/常见收集器方法.png)

案例：
```java
public void test1() {
    // 转collection
    Set<User> set = all.stream().collect(Collectors.toSet());
    List<Integer> list = all.stream().map(User::getId).collect(Collectors.toList());
    // 转map
    Map<String, User> map = all.stream().collect(Collectors.toMap(User::getSex, Function.identity()));
    // 转map，处理key相同的元素
    Map<String, User> map2 = all.stream().collect(Collectors.toMap(User::getSex, Function.identity(), (x, y) -> x));
    // 根据属性分组
    Map<String, List<User>> sexMap = all.stream().collect(Collectors.groupingBy(User::getSex));
}
```

# 并行流 与 串行流

# 新时间日期API

在旧版的java中，日期时间API存在诸多问题，其中有：  
1）非线程安全，java.util.Date 是非线程安全的，所有的日期类都是可变得，这是java日期类最大的问题之一  
2）设计很差，java的日期/时间类的定义并不一致，在 java.util 和 java.sql 的包中都要日期类，此外用于格式化和解析的类在java.text包中定义。Java.utail.Date同时包含日期和时间，而java.sql.Date仅包含日期，将其纳入java.sql包并不合理。另外这两个类都有相同的名字，这本身是一个非常糟糕的设计。  
3）时区处理麻烦，日期类并不提供国际化，没有时区支持，因此java引入了java.util.Calendar和java.util.TimeZone类，但他们同样存在上述所有问题。

**因此：Java8 通过发布新的Date-time API（JSR 310）来进一步加强对日期与时间的处理。**

Java8 在 java.Time 包下提供了很多新的API。以下两个比较重要的API：  
Local（本地）  简化了日期的处理，没有时区的问题。  
Zoned（时区）  通过制定的时区处理日期时间。  
新的java.time包覆盖了所有处理日期，时间，日期/时间，时区，时刻（instants），过程（during）与时钟（clock）的操作。

## 常用类简介

Instat：用于表示时间上的一个点，表示一个时间戳（精确到纳秒）
Period：用于计算日期间隔  
Duration：用于计算时间间隔  

LocalDate：表示日期  
LcalTime：表示时间  
LocalDateTime：表示日期+时间，相当于 LocalDate + LocalTime

Zoneld：时区  
ZonedDateTime：表示日期+时间+时区值

DateTimeFormatter：用于日期时间的格式化

## 本地化日期时间API

LocalDate：表示日期  
LcalTime：表示时间  
LocalDateTime：表示日期和时间，相当于 LocalDate + LocalTime

公共API：  
now()：静态方法，根据当前时间创建对象。  
of()：静态方法，根据指定的日期/时间创建对象。

LocalDate 与 LocalDateTime公共API：  
getYear()：获取年份  
getMonth()：获取月份，返回一个Month的枚举  
getMonthValue()：获取月份  
getDayOfMonth()：获取当前月份天数【1-31】  
getDayOfYear()：获取当前年份天数【1-366】  
getDayOfWeek()：获取星期几，返回一个DayOfWeek的枚举  
plus()：添加一个 Duration 或 Period  
minus()：删除一个 Duration 或 Period
plusDays(); plusWeeks(); plusMonths(); plusYears()：  
向当前对象，新增几天，几周，几月，几年。
minusDays(); minusWeeks(); minusMonths(); minusYears()：  
向当前对象，减去几天，几周，几月，几年。
withDayOfMonth()：修改当前月的天数  
withDayOfYear()：修改当前年的天数

LocalTime 与 LocalDateTime公共API：  
getHour()：获取小时  
getMinute()：获取分钟  
getSecond()：获取秒

## 时区日期时间API

## Instant 时间戳

Java8新提供Instant获取秒数，用于表示时间上的一个点，表示一个时间戳（精确到纳秒）
如果只是为了获取秒数或毫秒数，可以使用System.currentTimeMillis()。

用于“时间戳”的运算。它是以Unix元年(传统的设定为UTC时区1970年1月1日午夜时分)开始所经历的描述进行运算。

Instant API方法：  
getEpochSecond()：获取秒数  
toEpochMilli()：获取毫秒数  
getNano()：获取纳秒数  
System.currentTimeMillis()：获取时间戳，毫秒级别。

```java
Instant instant = Instant.now();
// 获取秒数
long currentSecond = instant.getEpochSecond();
// 获取毫秒数
long currentMilli = instant.toEpochMilli();
// 时间戳
long currentTimeMilli = System.currentTimeMillis();
System.out.println(currentSecond);
System.out.println(currentMilli);
System.out.println(instant);
System.out.println(currentTimeMilli);
```

## Period 日期间隔

Java8新提供Period来表示一个日期段，日期间隔。

Period API方法：  
Between(from, to)：	获取两个日期的间隔，返回Period实例  
getYears();			获取这段日期的相隔年数  
getMonths(); 		获取单纯月份比较，相隔几月  
getDays(); 			获取单纯天数比较，相隔几天

```java
// 1999-06-24
LocalDate from = LocalDate.of(1999, Month.MAY, 24);
// 2020-08-20
LocalDate to = LocalDate.of(2020, Month.JULY, 20);

Period period = Period.between(from, to);
int years = period.getYears(); // 这段日期的相隔年数
int months = period.getMonths(); // 单纯月份比较，相隔几月
int days = period.getDays(); // 单纯天数比较，相隔几天
```

## Duration 时间间隔

Java8新提供Duration来表示一个时间段，时间间隔。

Duration API方法：  
Between(from, to)：	获取两个日期的间隔，返回Duration实例  
toDays()：		获取这段时间的天数  
toHours()：		获取这段时间的小时  
toMinutes()：		获取这段时间的分钟数  
getSeconds()：	获取这段时间的秒数  
toMillis()：		获取这段时间的毫秒数  
toNanos()：		获取这段时间的纳秒数

```java
@Test
public void durationTest() {
    // 1999-06-24 00:00:00
    LocalDateTime from = LocalDateTime.of(1999, Month.MAY, 24, 0, 0, 0);
    // 2020-08-20 00:00:00
    LocalDateTime to = LocalDateTime.of(2020, Month.JULY, 20, 0, 0, 0);

    Duration duration = Duration.between(from, to);

    long days = duration.toDays();// 这段时间的天数
    long hours = duration.toHours();// 这段时间的小时
    long minutes  = duration.toMinutes();// 这段时间的分钟数
    long seconds = duration.getSeconds();// 这段时间的秒数
    long milliSeconds = duration.toMillis();// 这段时间的毫秒数
    long nanos = duration.toNanos();// 这段时间的纳秒数

    System.out.println(days);
    System.out.println(hours);
    System.out.println(minutes);
    System.out.println(seconds);
    System.out.println(milliSeconds);
    System.out.println(nanos);
}
```

## 解析与格式化

格式化时间：  
Java8新提供DateTimeFormatter来格式化时间。

解析时间：  
使用时间类LocalDate、LocalTime自带的parse()方法进行解析时间。

格式化时间demo：
```java
LocalDateTime now = LocalDateTime.now();
String s1 = DateTimeFormatter.BASIC_ISO_DATE.format(now);
String s2 = DateTimeFormatter.ISO_DATE_TIME.format(now);
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
String format = formatter.format(now);
```

解析时间Demo：
```java
LocalDate localDate = LocalDate.parse("2020-10-01");
LocalDateTime localDateTime = LocalDateTime.parse("2020-10-01T15:15:15");
LocalDateTime copyLocalDateTime = LocalDateTime.parse("2020-10-01 15:15:15", DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM));
```

# 接口中的默认方法 与 静态方法

## 默认方法

Java8新增了接口的默认方法。  
简单说：默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。

为什么要有这个特性：  
首先，之前的接口是个双刃剑，好处是面向抽象而不是面向具体编程，缺陷是，当需要修改接口时候，需要修改全部实现该接口的类。java8之前的集合框架没有foreach方法，通常能想到的解决方法是在jdk里给相关的接口添加新的方法及实现。  
然而，对于已经发布的版本，是没法再给接口添加新方法的同时不影响已有的实现。所以引进的默认方法，目的是为了解决接口的修改与现有的实现不兼容问题。

接口默认方法语法格式：
```java
public interface MyInterface {

    default String getStr() {
        return "hello interface default method";
    }

}
```

## 默认方法的问题

如果一个接口定义了一个默认方法，而另外一个父类或接口中又定义了一个同名的方法时：

接口冲突：
如果一个接口提供了一个默认方法，而另一个接口也提供了一个具有相同名称和参数列表的方法（不管方法是否默认方法），一个类实现了这两个接口，那么必须覆盖该方法来解决冲突。

类优先原则：
如果一个接口提供了一个默认方法，而父类又定义了一个同名的方法时，在子类中，那么接口中具有相同名称和参数的默认方法会被忽略，生效的是父类的同名方法。

接口冲突案例：
```java
public interface Dog {

    default void call() {
        System.out.println("我是狗，汪汪汪");
    }

}

public interface Cat {

    default void call() {
        System.out.println("我是猫，喵喵喵");
    }

}

public class Demo implements Dog, Cat {

    public static void main(String[] args) {
        Demo demo = new Demo();
        demo.call();
    }

    @Override
    public void call() {
        Dog.super.call();
        Cat.super.call();
        System.out.println("我是demo");
    }
}
```

类优先原则：
```java
public abstract class BaseClass {

    public void call() {
        System.out.println("我是baseClass");
    }

}

public class Demo extends BaseClass implements Dog, Cat {

    public static void main(String[] args) {
        Demo demo = new Demo();
        demo.call();
    }
    
}
```

## 静态方法

Java8的另一个特性是接口可以声明（并且可以提供实现）静态方法。

接口静态方法语法格式：
```java
public interface MyInterface {

    static String getStr() {
        return "hello interface static method";
    }

}
```

# Optional 类

## Optional 类简介

Optional 类（java.util.Optional）是一个可以为null的容器对象，如果值存在则isPresent()方法会返回true，调用get方法会返回该对象。

Optional 类是个容器：它可以保存类型T的值，或者仅仅保存null。Optional 类提供很多有用的方法，这样我们就不用显式进行空值检测。

Optional 类的引入，可以很好的解决空指针异常。
Optional 类可以很好的判断一个值存在或者不存在。

## 常用方法

```
Optional.get()               获取Optional容器包含的值，如果值为null，抛出异常

Optional .of(T t)  			根据t创建Optional 实例，如果t为null，抛出空指针异常
Optional.ofNullable(T t)		若t不为空，创建Optional 实例，否则返回空的Optional 实例
Optional .empty()			创建一个空的Optional 实例

Optional .isPresent() 		判断是否包含值，包含返回true，否则返回false
OrElse(T t)				如果调用对象包含值，返回该值，否则返回t
OrElseGet(supplier s)		如果调用对象包含值，返回该值，否则返回s获取的值
Map(function f)			有值对其处理返回处理后的Optional，否则返回Optional.empty()
flatMap(function f)			与map类型，要求返回Optional
```

案例：
```java
    @Test
    public void methodDemo() {
        // optional.of创建optional实例，t不能为空，为null，抛出异常
        Optional<Integer> integerOptional = Optional.of(1);
        // optional.ofNullable创建optional实例，t如果为空，返回一个空的optional
        Optional<User> userOptional = Optional.ofNullable(null);
        // optional.empty创建一个空值得optional实例
        Optional<Object> empty = Optional.empty();


    //        // get，optional如果值为空，抛出异常
    //        integerOptional.get();
    //        userOptional.get();
    //        empty.get();

        System.out.println(userOptional.isPresent());

        // 如果值不为空，则返回，如果为空，则返回给定的值
        Integer integer = integerOptional.orElse(3);
        User user = userOptional.orElse(new User());
        Object o = empty.orElse(new BigDecimal("100"));

        System.out.println(integer);
        System.out.println(user);
        System.out.println(o);
    }
```