---
layout: post
title: Ninety-Nine Scala Problems-14&15_Duplicate the elements of a list
excerpt: "the 14th and 15th of Ninety-Nine Scala Problems: Modified run-length encoding. scala-99题的第14、15道"
categories: Ninety-Nine-Scala-Problems
tags: [scala]
comments: true
---

题目要求：1
Example:

```scala
scala> duplicate(List('a, 'b, 'c, 'c, 'd))
res0: List[Symbol] = List('a, 'a, 'b, 'b, 'c, 'c, 'c, 'c, 'd, 'd)
```

解法：

```
def duplicate[A](ls:List[A]):List[A]={
  var list:List[A]=Nil
  ls.foreach(e=> for(i<-0 to 1){list=list:::List(e)} )
  list
}
```


题目要求：2
Example:

```scala
scala> duplicateN(3, List('a, 'b, 'c, 'c, 'd))
res0: List[Symbol] = List('a, 'a, 'a, 'b, 'b, 'b, 'c, 'c, 'c, 'c, 'c, 'c, 'd, 'd, 'd)
```

解法：

```
def duplicateN[A](N:Int,ls:List[A]):List[A]={
  var list:List[A]=Nil
  ls.foreach(e=> for(i<-0 to N-1){list=list:::List(e)} )
  list
}
```

