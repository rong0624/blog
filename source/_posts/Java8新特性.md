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

**其中Lambda 表达式与Stream API最为核心的。**

# Lambda表达式

## 什么是Lambda 表达式

Lambda 表达式，也可称为闭包，一个匿名函数，它是推动 Java 8 发布的最重要新特性。  
Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）。  
使用 Lambda 表达式可以使代码变的更加简洁紧凑。

## Lambda 表达式

Lambda 表达式的基础语法：  
```Java8中引入了一个新操作符：“->”该操作符被称为剪头操作符或 Lambda 操作符。```

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

```java
1）Consumer<T>：消费型接口
Void accept(T t)

2）Supplier<T>：供给型接口
T get();

3）Function<T, R>：函数型接口
R apply(T t);

4）Predicate<T>：断言型接口
Boolean test(T t);
```
