---
layout: post
title: Ninety-Nine Scala Problems-12_Decode a run-length encoded list
excerpt: "the 12th of Ninety-Nine Scala Problems: Modified run-length encoding. scala-99题的第12道"
categories: Ninety-Nine-Scala-Problems
tags: [scala]
comments: true
---

题目要求：
Given a run-length code list generated as specified in problem P10, construct its uncompressed version.

Example:
```scala
scala> decode(List((4, 'a), (1, 'b), (2, 'c), (2, 'a), (1, 'd), (4, 'e)))
res0: List[Symbol] = List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e)
```

解法：
List进行遍历的时候将每个元组的字母n次添加到list中

```
  def decode[A](ls:List[(Int,Symbol)]):List[Symbol]={
    var list:List[Symbol]=Nil
    ls.foreach{e=>
      for(i<-0 to e._1-1) {
        list=list:::List(e._2)
      }
    }
    list
  }

  def main(args: Array[String])= {
    println(decode(List((1,'a),(2,'b),(3,'c),(2,'d))))
  }
```
