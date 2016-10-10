---
layout: post
title: Ninety-Nine Scala Problems-11_Modified run-length encoding
excerpt: "the 11th of Ninety-Nine Scala Problems: Modified run-length encoding. scala-99题的第11道"
categories: Ninety-Nine Scala Problems
tags: [scala]
comments: true
---

题目要求：
Modify the result of problem P10 in such a way that if an element has no duplicates it is simply copied into the result list. Only elements with duplicates are transferred as (N, E) terms.

Example:
```scala
scala> encodeModified(List('a, 'a, 'a, 'a, 'b, 'c, 'c, 'a, 'a, 'd, 'e, 'e, 'e, 'e))
res0: List[Any] = List((4,'a), 'b, (2,'c), (2,'a), 'd, (4,'e))
```

解法：
spanList通过递归将列表拆分成多个元素相同的列表，
(takes,lefts)=curList span(_==curList.head)  根据条件将列表分成两部分

```
def encodeModified[A](ls: List[A]) =  {
    def spanList[A](curList:List[A]):List[List[A]]={
      if(curList.isEmpty) List(List())
      else{
        val list=Nil
        val (takes,lefts)=curList span(_==curList.head)
        if(lefts==Nil) List(takes)
        else List(takes):::spanList(lefts)
       }
    }

  spanList(ls).map(e=> if(e.length>1){(e.length,e.head)} else{e.head} )
}

def main(args: Array[String])= {
  println(encodeModified(List(1,1,2,2,3,4,4,5,7)))
}
```
结果
List((2,1), (2,2), 3, (2,4), 5, 7)

<a href="http://aperiodic.net/phil/scala/s-99/p11.scala"> 参考答案 </a>
