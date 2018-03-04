---
layout: post
title: Akka Typed学习(1)-Hello World示例
excerpt: "Akka Typed学习(1)-Hello World示例"
categories: scala
tags: 
- Akka Typed
- scala

comments: true
---

* Table of Contents
{:toc}


Akka-actor 编程模型由于缺乏类型安全而被诟病已久，最近akka推出了Akka Typed, 
为actor的世界带来类型安全。


在一系列博客文章中，我们将展示新的API，并为熟悉经典无类型actor的用户轻松介绍Akka Typed。
 我们将特别介绍有类型和无类型的actor之间的共同点和差异


当我们谈及经典的无类型actor的时候，我们的意思是这种类型的ActorRef无法传达出任何
可以通过该ActorRef发送哪些类型消息以及目的地Actor的类型的信息。这就使得程序阅读困难
并且编写易于出错，尤其是前者，可读性方面尤为重要。 Akk Typed在ActorRef中增加了类型参数:

```

val greeter: ActorRef[Greeter.Command] = 
  ctx.spawn(Greeter.greeterBehavior, "greeter") 

```


## Current Status

首先，如果您有更好的想法，API调整仍然是可行的。
 它可以用于普通的actor，包括像

```

tell, ask
spawning child actors, supervision and watch
coexistence of typed and untyped actors in same ActorSystem

```

目前我们所知道缺少或不完整的：

```
remote messaging
receptionist pattern (a typed replacement for actorSelection)
persistence
integration with other modules, such as Streams, Cluster, Distributed Data
the process DSL
logging API
performance optimizations

```

上述一些限制可能会通过集成无类型的actors来解决，
但这些事情当然会被实现。 
我们的目标就是使Akka Typed 完全替换现有的无类型actor。 然而，无类型的
actor不会很快就消失，所以有类型和无类型的将能够共存。


## Hello World

本文的例子将采用scala呈现，java的例程参考
<a href="https://github.com/patriknw/akka-typed-blog" > 
    patriknw/akka-typed-blog
</a>

下面看一个经典的简单hello world actor 然后我们看看如何用Akka Typed实现。

```
import akka.actor.Actor
import akka.actor.ActorRef
import akka.actor.Props

object Greeter1 {
  case object Greet
  final case class WhoToGreet(who: String)
}

class Greeter1 extends Actor {
  import Greeter1._

  private var greeting = "hello"

  override def receive = {
    case WhoToGreet(who) =>
      greeting = s"hello, $who"
    case Greet =>
      println(greeting)
  }
}
```


 <font size="4"> package scala.collection </font>

 ![这里写图片描述](http://img.blog.csdn.net/20161021123146101)


 <font size="4"> package scala.collection.immutable </font>

 ![这里写图片描述](http://img.blog.csdn.net/20161021123225483)


  <font size="4"> package scala.collection.mutable </font>

 ![这里写图片描述](http://img.blog.csdn.net/20161021123239727)


## 基本集合

### List

List是scala中最常用的集合之一

```
scala> val ls = List(1,2,3,4,5)
ls: List[Int] = List(1, 2, 3, 4, 5)
```

List可以使用 ::: 进行连接，也可以使用 e :: ls 进行添加元素，
同样也可以使用head 和 tail获取头元素和去掉头元素的剩下的List

```
scala> ls.tail
res0: List[Int] = List(2, 3, 4, 5)

scala> ls.head
res1: Int = 1

scala> 0::ls
res2: List[Int] = List(0, 1, 2, 3, 4, 5)

scala> ls ::: List(6,7,8)
res3: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8)

```

