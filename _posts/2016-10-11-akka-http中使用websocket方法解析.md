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

### 模型 model

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

### Server API


WebSocket API的入口点是合成的UpgradeToWebSocket头信息，如果Akka HTTP遇到WebSocket 的upquest请求，则会将其添加到请求中。

WebSocket规范要求通过将特殊目的HTTP头放入HTTP升级的请求和响应来协商WebSocket连接的细节。在Akka HTTP中，WebSocket握手的这些HTTP级别详细信息对应用程序是隐藏的，不需要手动管理。

相反，合成的UpgradeToWebSocket表示有效的WebSocket升级请求。应用程序可以通过查找UpgradeToWebSocket标头来检测WebSocket升级请求。它可以选择接受升级并通过使用由UpgradeToWebSocket.handleMessagesWith方法之一生成的HttpResponse响应该请求来启动WebSocket连接。在其最通常的形式中，这个方法期望两个参数：第一，handler Flow[Message, Message, Any]，将用于处理此连接上的WebSocket消息。其次，应用程序可以通过检查UpgradeToWebSocket.requestedProtocols的值来选择一个所提出的应用程序级子协议，并将所选的协议值传递给handleMessages。

### Handling Messages

消息处理程序应被实现为Flow[Message, Message, Any]。 对于典型的请求 - 响应场景，这非常适合，并且可以通过使用Flow [Message] .map或Flow [Message] .mapAsync从简单的函数构造这样的Flow。

还有其他用例，例如。 在server-push模型中，其中服务器消息被自发地发送，或者在输入和输出不是逻辑连接的真正的bi-directional场景中。 在这些情况下将handler提供为Flow可能不适合。 另一种方法,UpgradeToWebSocket.handleMessagesWithSinkSource，其允许独立地传递输出生成Source [Message，Any]和输入接收Sink [Message，Any]。

注意，需要一个处理程序来消费每个消息的数据流，以便为新消息留出空间。 否则，后续消息可能被阻塞，并且该方向上的消息业务将停止。

### Example

WebSocket请求就像任何其他请求一样。 在示例中，对 / greeter的请求应为WebSocket请求：

```
val requestHandler: HttpRequest => HttpResponse = {
  case req @ HttpRequest(GET, Uri.Path("/greeter"), _, _, _) =>
    req.header[UpgradeToWebSocket] match {
      case Some(upgrade) => upgrade.handleMessages(greeterWebSocketService)
      case None          => HttpResponse(400, entity = "Not a valid websocket request!")
    }
  case r: HttpRequest =>
    r.discardEntityBytes() // important to drain incoming HTTP Entity stream
    HttpResponse(404, entity = "Unknown resource!")
}

```

它使用路径上的模式匹配，然后检查请求以查询UpgradeToWebSocket标头。 如果找到这样的header，它被用于通过传递WebSocket消息的处理程序到handleMessages方法来生成响应。 如果没有找到这样的报头，则生成“400 Bad Request”响应。

传递的处理程序需要文本消息，其中每个消息都应包含一个名字，然后用另一个包含greeting的文本消息进行响应：

```
// The Greeter WebSocket Service expects a "name" per message and
// returns a greeting message for that name
val greeterWebSocketService =
  Flow[Message]
    .mapConcat {
      // we match but don't actually consume the text message here,
      // rather we simply stream it back as the tail of the response
      // this means we might start sending the response even before the
      // end of the incoming message has been received
      case tm: TextMessage => TextMessage(Source.single("Hello ") ++ tm.textStream) :: Nil
      case bm: BinaryMessage =>
        // ignore binary messages but drain content to avoid the stream being clogged
        bm.dataStream.runWith(Sink.ignore)
        Nil
    }

```

### Routing support

如果请求是WebSocket请求，则路由DSL提供handleWebSocketMessages指令来安装WebSocket处理程序。 否则，指令拒绝请求。

这里是上面的简单请求处理程序重写为路由：

```
def greeter: Flow[Message, Message, Any] =
  Flow[Message].mapConcat {
    case tm: TextMessage =>
      TextMessage(Source.single("Hello ") ++ tm.textStream ++ Source.single("!")) :: Nil
    case bm: BinaryMessage =>
      // ignore binary messages but drain content to avoid the stream being clogged
      bm.dataStream.runWith(Sink.ignore)
      Nil
  }
val websocketRoute =
  path("greeter") {
    handleWebSocketMessages(greeter)
  }
 
// tests:
// create a testing probe representing the client-side
val wsClient = WSProbe()
 
// WS creates a WebSocket request for testing
WS("/greeter", wsClient.flow) ~> websocketRoute ~>
  check {
    // check response for WS Upgrade headers
    isWebSocketUpgrade shouldEqual true
 
    // manually run a WS conversation
    wsClient.sendMessage("Peter")
    wsClient.expectMessage("Hello Peter!")
 
    wsClient.sendMessage(BinaryMessage(ByteString("abcdef")))
    wsClient.expectNoMessage(100.millis)
 
    wsClient.sendMessage("John")
    wsClient.expectMessage("Hello John!")
 
    wsClient.sendCompletion()
    wsClient.expectCompletion()
  }

```

该示例还包括演示testkit对WebSocket服务的支持的代码。 它允许创建WebSocket请求以使用WS运行路由，可以用于提供一个模拟的WebSocket探针，允许手动测试WebSocket处理程序的行为，如果请求被接受。
