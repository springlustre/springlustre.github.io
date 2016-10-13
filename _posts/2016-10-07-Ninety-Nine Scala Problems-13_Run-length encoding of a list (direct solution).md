---
layout: post
title: Ninety-Nine Scala Problems-13_Run-length encoding of a list (direct solution)
excerpt: "the 13th of Ninety-Nine Scala Problems: Modified run-length encoding. scala-99题的第13道"
categories: Ninety-Nine Scala Problems
tags: [scala]
comments: true
---

题目要求：
Implement the so-called run-length encoding data compression method directly. I.e. don't use other methods you've written (like P09's pack); do all the work directly.

Example:
```scala
scala> encodeDirect(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[(Int, Symbol)] = List((4,'a), (1,'b), (2,'c), (2,'a), (1,'d), (4,'e))
```

解法：

```
  def encodeDirect[A](ls:List[A]):List[(Int,A)]={
      if(ls==Nil) Nil
      else {
        val l = ls.dropWhile(_ == ls.head)
        List((ls.length - l.length, ls.head)) ::: encodeDirect(l)
      }
  }

  def main(args: Array[String])= {
    println(encodeDirect(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e)))
  }
```

或者

```

   def encodeDirect[A](ls: List[A]): List[(Int, A)] =
    if (ls.isEmpty) Nil
    else {
      val (packed, next) = ls span { _ == ls.head }
      (packed.length, packed.head) :: encodeDirect(next)
    }

```

