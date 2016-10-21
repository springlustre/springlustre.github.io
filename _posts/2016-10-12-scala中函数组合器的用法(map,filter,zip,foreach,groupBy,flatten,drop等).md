---
layout: post
title: scala中函数组合器的用法(map,filter,zip,foreach,groupBy,flatten,drop等)
excerpt: "scala中函数组合器的用法(map,filter,zip,foreach,groupBy,flatten,drop等)"
categories: scala
tags: 
- scala
- 函数组合器

comments: true
---

* Table of Contents
{:toc}

本文主要记录了scala学习中的常用函数组合器，包括map,filter,zip,foreach,groupBy,flatten,drop等。


## map

map方法可以将某个函数应用到集合中的每个元素并产出其结果的集合。举例：

```
val names = List("a","b","c")
names.map(_.toUpperCase)
```

得到结果
```
List("A","B","C")
```

## foreach

foreach和map类似，只不过它没有返回值，foreach只要是为了对参数进行作用。比如：

```
List(1,2,3,4,5).foreach{x=>
   println(x)
}

输出：

1
2
3
4
5
```

## flatten

flatten的作用是可以把嵌套的结构展开，例如

```
scala> List(List(1,2),List(3,4)).flatten
res0: List[Int] = List(1, 2, 3, 4)
```

## flatmap

flatMap结合了map和flatten的功能。接收一个可以处理嵌套列表的函数，然后把返回结果连接起来。

```
scala> List(List(1,2),List(3,4)).flatMap(x=>x.map(x=>x*2))
res5: List[Int] = List(2, 4, 6, 8)
```


## filter

滤除掉使函数返回false的元素

```
scala> def isEven(i: Int): Boolean = i % 2 == 0
isEven: (i: Int)Boolean

scala> List(1,2,3,4).filter(isEven _)
res6: List[Int] = List(2, 4)
```

## zip 

zip方法将两个集合结合在一起,例如：

```
scala>  List('a,'b,'c).zip(List(1,2,3))
res32: List[(Symbol, Int)] = List(('a,1), ('b,2), ('c,3))
```

## zipWithIndex

将集合中的元素和对应的下标结合再一起,下标从0开始

```
scala> List(2,3,4,5).zipWithIndex
res33: List[(Int, Int)] = List((2,0), (3,1), (4,2), (5,3))
```

## groupBy

对集合中的元素进行分组操作，结果是得到一个Map，举例

```
scala> List((1,2),(1,3),(2,3),(3,3),(3,4)).groupBy(_._1)
res1: scala.collection.immutable.Map[Int,List[(Int, Int)]] = Map(2 -> List((2,3)), 1 -> List((1,2), (1,3)), 
      3 -> List((3,3), (3,4)))
```

## drop

去掉集合前面的n个元素

```
scala> List(1,2,3,4,5,6,7).drop(3)
res2: List[Int] = List(4, 5, 6, 7)
```

dropRight(n) 是去掉后面的n个元素

```
scala> List(1,2,3,4,5,6,7).dropRight(3)
res3: List[Int] = List(1, 2, 3, 4)
```


## patch

对于可变的数据结构进行批量替换，返回新的copy，原数据不变

```
scala> val a = List(1,2,3)
a: List[Int] = List(1, 2, 3)

scala> val b = List(4,5)
b: List[Int] = List(4, 5)

scala> a patch(0,b,2)
res1: List[Int] = List(4, 5, 3)

scala> a patch(1,b,2)
res2: List[Int] = List(1, 4, 5)

```

