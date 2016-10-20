---
layout: post
title: akka-http中使用websocket方法详解
excerpt: "akka-http中使用websocket方法祥解"
categories: scala
tags: 
- scala
- akka-http
- websocket

comments: true
---

* Table of Contents
{:toc}



## WebSocket介绍

WebSocket是HTML5出的协议，是一个新协议，跟HTTP协议基本没有关系，但是它完成一部分握手是通过http协议，可以说它是HTTP协议上的一种补充。

在WebSocket出现之前，我们有一些其它的实时通讯方案，比较常用的有轮询，长连接等。

轮询是最早的一种实现实时web应用的方案；原理比较简单易懂，就是客户端以一定的时间间隔向服务器发送请求，
以频繁请求的方式来保持客户端和服务器端的数据同步。但是问题也很明显：当客户端以固定频率向服务器端发送请
求时，服务器端的数据可能并没有更新，这样会带来很多无谓的请求，浪费带宽，效率低下。

长连接是对定时轮询的改进和提高，目地是为了降低无效的网络传输。当服务器端没有数据更新的时候，连接会保持一
段时间周期直到数据或状态改变或者时间过期，来减少无效的客户端和服务器间的交互。当如果服务端的数据变更非常
频繁的话，这种机制和定时轮询比较起来没有本质上的性能的提高。

而WebSocket则是先通过HTTP/HTTPS协议进行握手后创建一个用于交换数据的TCP连接，此后服务端与客户端通过此
TCP连接进行实时通信。数据传输对客户端和服务端而言都是一个双工通信通道。

ws是普通的WebSocket通信协议，而wss是安全的WebSocket通信协议(就像HTTP与HTTPS之间的差异一样)。在缺省情况下，ws的端口是80而wss的端口是443。




## akka-http中 WebSocket的使用

Akka HTTP提供了基于流的WebSocket协议实现，隐藏了底层二进制框架线协议的底层细节，并提供了一个简单的API来使用WebSocket实现服务。

Akka HTTP为这个抽象提供了一个直接的模型：

```
/**
 * The ADT for WebSocket messages. A message can either be a binary or a text message.
 */
sealed trait Message
 
/**
 * Represents a WebSocket text message. A text message can either be a [[TextMessage.Strict]] in which case
 * the complete data is already available or it can be [[TextMessage.Streamed]] in which case [[textStream]]
 * will return a Source streaming the data as it comes in.
 */
sealed trait TextMessage extends Message {
  /**
   * The contents of this message as a stream.
   */
  def textStream: Source[String, _]
}
sealed trait BinaryMessage extends Message {
  /**
   * The contents of this message as a stream.
   */
  def dataStream: Source[ByteString, _]
}

```
消息的数据作为流提供的，因为WebSocket消息没有预定义的大小，并且可以（在理论上）无限长。 然而，对于WebSocket连接的每个方向只能打开一个消息，使得许多应用级协议将希望利用对（小）消息的描述来传送单个应用级数据单元，例如“一个事件”或“一个聊天消息“。

许多消息足够小以便一次发送或接收。作为优化的机会，该模型为每种类型的消息提供了严格的子类，其包含作为严格（即非流）字节字符串或字符串的数据。

当从网络连接接收数据时，WebSocket实现尽可能地尝试创建严格消息，即当在一个块中接收到完整数据时。然而，通过网络连接和通过各种流抽象层的消息的实际分块从应用的角度来看不是确定性的。因此，应用程序代码必须能够处理流和严格消息，并且不期望某些消息是严格的。 （特别注意，对localhost的测试将与对通过物理网络连接接收数据的远程对等体的测试不同）。

对于发送数据，使用TextMessage.apply（text：String）创建一个严格的消息，当完整的消息已经被汇编时，
这通常是自然的选择。否则，使用TextMessage.apply（textStream：Source [String，Any]）从Akka Stream源创建流式传输消息。



RDD本质上是一个内存数据集，在访问RDD时，指针只会指向与操作相关的部分。例如存在一个面向列的数据结构，其中一个实现为Int的数组，另一个实现为Float的数组。如果只需要访问Int字段，RDD的指针可以只访问Int数组，避免了对整个数据结构的扫描。

RDD将操作分为两类：transformation与action。无论执行了多少次transformation操作，RDD都不会真正执行运算，只有当action操作被执行时，运算才会触发。而在RDD的内部实现机制中，底层接口则是基于迭代器的，从而使得数据访问变得更高效，也避免了大量中间结果对内存的消耗。

在实现时，RDD针对transformation操作，都提供了对应的继承自RDD的类型，例如map操作会返回MappedRDD，而flatMap则返回FlatMappedRDD。当我们执行map或flatMap操作时，不过是将当前RDD对象传递给对应的RDD对象而已.

以下面一个按 A-Z 首字母分类，查找相同首字母下不同姓名总个数的例子来看一下 RDD 是如何运行起来的。
![Elaphurus davidianus](http://images0.cnblogs.com/blog/107289/201508/111610089731588.jpg)

步骤 1 ：创建 RDD  

上面的例子除去最后一个 collect 是个动作，不会创建 RDD 之外，前面四个转换都会创建出新的 RDD 。因此第一步就是创建好所有 RDD( 内部的五项信息 ) 。

步骤 2 ：创建执行计划 

Spark 会尽可能地管道化，并基于是否要重新组织数据来划分阶段 (stage) ，例如本例中的 groupBy() 转换就会将整个执行计划划分成两阶段执行。最终会产生一个 DAG(directed acyclic graph ，有向无环图 ) 作为逻辑执行计划。
![Elaphurus davidianus](http://images0.cnblogs.com/blog/107289/201508/111610154426712.jpg)

步骤 3 ：调度任务  

将各阶段划分成不同的 任务 (task) ，每个任务都是数据和计算的合体。在进行下一阶段前，当前阶段的所有任务都要执行完成。因为下一阶段的第一个转换一定是重新组织数据的，所以必须等当前阶段所有结果数据都计算出来了才能继续。


### 懒惰计算

懒惰计算（lazy evaluation）：Spark在遇到 Transformations操作时只会记录需要这样的操作，并不会去执行，需要等到有Actions操作的时候才会真正启动计算过程进行计算。（不像Python和matlab马上执行）。



## Spark 安装

### 安装jdk和scala

下载安装JDK：https://www.oracle.com/index.html 并配置环境变量，不再详述。

下载Scala： http://www.scala-lang.org/。
解压缩：tar –zxvf scala-2.10.6.tgz。
进入sudo vim /etc/profile 在下面添加路径：
```
SPARK_HOME=/home/spark/spark-1.5.1-bin-hadoop2.6
PATH=$PATH:${SCALA_HOME}/bin
``` 
使修改生效source /etc/profile。
在命令行输入scala测试。

进入spark的bin目录
输入run-example org.apache.spark.examples.SparkPi 
出现如下结果则安装成功

![Elaphurus davidianus](../../../images/2016_10_16_1.png)


