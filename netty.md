参考：http:\/\/ifeve.com\/netty-in-action\/

Netty系列之Netty高性能之道 http:\/\/www.infoq.com\/cn\/articles\/netty-high-performance

Preface

The Problem

Nowadays we use general purpose applications or libraries to communicate with each other. For example, we often use an HTTP client library to retrieve information from a web server and to invoke a remote procedure call via web services. However, a general purpose protocol or its implementation sometimes does not scale very well. It is like we don't use a general purpose HTTP server to exchange huge files, e-mail messages, and near-realtime messages such as financial information and multiplayer game data. What's required is a highly optimized protocol implementation which is dedicated to a special purpose. For example, you might want to implement an HTTP server which is optimized for AJAX-based chat application, media streaming, or large file transfer. You could even want to design and implement a whole new protocol which is precisely tailored to your need. Another inevitable case is when you have to deal with a legacy proprietary protocol to ensure the interoperability with an old system. What matters in this case is how quickly we can implement that protocol while not sacrificing the stability and performance of the resulting application.

前言

问题

现在，我们使用应用程序或库的一般目的是相互通信。例如，我们经常使用HTTP客户端库从Web服务器检索信息和调用远程过程调用通过Web服务。然而，一个通用的协议或它的实现有时可扩展性不是很好。这就像我们不使用通用的HTTP服务器来交换大文件，电子邮件，和近实时信息，如财务信息和游戏数据。所需要的是一个高度优化的协议实现，这是专门为一个特殊的目的。例如，你可能想实现一个HTTP服务器，它是优化基于AJAX的聊天应用，流媒体，或大文件传输。你甚至可以设计和实现一个新的协议，这正是根据你的需要精确定制。另一个不可避免的情况是当你必须处理一个遗留的专有协议，以确保与旧系统的互操作性。在这种情况下，重要的是我们如何快速地实现该协议，同时不牺牲所得到的应用程序的稳定性和性能。

The Solution

_[The Netty project](http://netty.io/)_ is an effort to provide an asynchronous event-driven network application framework and tooling for the rapid development of maintainable high-performance · high-scalability protocol servers and clients.

In other words, Netty is an NIO client server framework which enables quick and easy development of network applications such as protocol servers and clients. It greatly simplifies and streamlines network programming such as TCP and UDP socket server development.

'Quick and easy' does not mean that a resulting application will suffer from a maintainability or a performance issue. Netty has been designed carefully with the experiences earned from the implementation of a lot of protocols such as FTP, SMTP, HTTP, and various binary and text-based legacy protocols. As a result, Netty has succeeded to find a way to achieve ease of development, performance, stability, and flexibility without a compromise.

Some users might already have found other network application framework that claims to have the same advantage, and you might want to ask what makes Netty so different from them. The answer is the philosophy it is built on. Netty is designed to give you the most comfortable experience both in terms of the API and the implementation from the day one. It is not something tangible but you will realize that this philosophy will make your life much easier as you read this guide and play with Netty.

解决方案

_[The Netty project](http://netty.io/)_ 致力于提供一个异步事件驱动网络应用框架，它为快速发展的服务器和客户端之间提供可维护高性能·高扩展性协议工具。

换句话说，Netty是一个NIO客户端\/服务器框架，使如服务器和客户端协议的网络应用开发快速且容易。它大大简化高效了网络编程，如TCP和UDP套接字服务器的开发。

“快速和简单”并不意味着一个最终的应用程序将遭受一个可维护性或性能问题。Netty已被精心设计，从大量的协议，如FTP、SMTP、HTTP的实施获得的经验，以及各种二进制和文本的传统协议。因此，Netty已成功地找到一种方法来达到缓解开发，性能，稳定性，灵活性，并不妥协。

有些用户可能已经找到了其他的网络应用框架，声称有同样的优势，你可能想问是什么让Netty如此不同。答案是它是建立在哲学上的。Netty设计，从一开始在API和实现上都给你最舒适的体验。它不是有形的东西但你会认识到，当你阅读本指南并使用Netty时，这种哲学将使你的生活更容易。

Getting Started

This chapter tours around the core constructs of Netty with simple examples to let you get started quickly. You will be able to write a client and a server on top of Netty right away when you are at the end of this chapter.

If you prefer top-down approach in learning something, you might want to start from Chapter 2, Architectural Overview and get back here.

入门

本章是用简单的例子游览Netty核心构建让你快速上手。你可以在本章的结尾通过Netty写一个客户端和一个服务器。

如果你喜欢自上而下的学习方法，你可能会想从2章开始，结构概述，回到这里。

Before Getting Started

The minimum requirements to run the examples which are introduced in this chapter are only two; the latest version of Netty and JDK 1.6 or above. The latest version of Netty is available in [the project download page](http://netty.io/downloads.html). To download the right version of JDK, please refer to your preferred JDK vendor's web site.

As you read, you might have more questions about the classes introduced in this chapter. Please refer to the API reference whenever you want to know more about them. All class names in this document are linked to the online API reference for your convenience. Also, please don't hesitate to [contact the Netty project community](http://netty.io/community.html) and let us know if there's any incorrect information, errors in grammar and typo, and if you have a good idea to improve the documentation.

在开始之前

### Writing a Discard Server

......

1. DiscardServerHandler extends [ChannelInboundHandlerAdapter](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandlerAdapter.html), which is an implementation of [ChannelInboundHandler](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandler.html). [ChannelInboundHandler](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandler.html) provides various event handler methods that you can override. For now, it is just enough to extend [ChannelInboundHandlerAdapter](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandlerAdapter.html) rather than to implement the handler interface by yourself.
2. We override the channelRead\(\) event handler method here. This method is called with the received message, whenever new data is received from a client. In this example, the type of the received message is [ByteBuf](http://netty.io/4.0/api/io/netty/buffer/ByteBuf.html).
3. To implement the DISCARD protocol, the handler has to ignore the received message. [ByteBuf](http://netty.io/4.0/api/io/netty/buffer/ByteBuf.html) is a reference-counted object which has to be released explicitly via the release\(\) method. Please keep in mind that it is the handler's responsibility to release any reference-counted object passed to the handler. Usually, channelRead\(\) handler method is implemented like the following:

  `@Override
  public void channelRead(ChannelHandlerContext ctx, Object msg) {
  try {
  // Do something with msg
  } finally {
  ReferenceCountUtil.release(msg);
  }
  }`

4. The exceptionCaught\(\) event handler method is called with a Throwable when an exception was raised by Netty due to an I\/O error or by a handler implementation due to the exception thrown while processing events. In most cases, the caught exception should be logged and its associated channel should be closed here, although the implementation of this method can be different depending on what you want to do to deal with an exceptional situation. For example, you might want to send a response message with an error code before closing the connection.


1. DiscardServerHandler继承[ChannelInboundHandlerAdapter](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandlerAdapter.html)，这是一个实现接口[ChannelInboundHandler](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandler.html)。[ChannelInboundHandler](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandler.html)提供不同的事件处理程序方法，你可以去重写它们。现在，使用继承[ChannelInboundHandlerAdapter](http://netty.io/4.0/api/io/netty/channel/ChannelInboundHandlerAdapter.html)这已经足够，而不是自己来实现处理器接口。

2. 我们重写channelRead\(\)事件处理程序方法。每当从客户端接收到新的数据，此方法被调用。在这个例子中，接收到的消息的类型是ByteBuf。

3. 要实现DISCARD协议，handler必须忽略接收的消息。[ByteBuf](http://netty.io/4.0/api/io/netty/buffer/ByteBuf.html)是引用计数的对象通过显式release\(\)方法已被释放。请记住，它是handler的责任，释放任何传递给handler的引用计数的对象。通常，channelread\(\)处理实现方法如下

4. exceptioncaught\(\)事件处理程序方法在抛出异常时调用，由Netty由于I\/O错误或handler的实现类在处理事件时引发的异常。在大多数情况下，捕获的异常应该被记录，并且它的相关的通道应该被关闭，虽然这个方法的实现可能会有所不同，这取决于你想做什么来处理一个异常的情况。例如，您可能希望在关闭连接之前发送一个错误代码的响应消息

  ......


1. [NioEventLoopGroup](http://netty.io/4.0/api/io/netty/channel/nio/NioEventLoopGroup.html) is a multithreaded event loop that handles I\/O operation. Netty provides various [EventLoopGroup](http://netty.io/4.0/api/io/netty/channel/EventLoopGroup.html) implementations for different kind of transports. We are implementing a server-side application in this example, and therefore two [NioEventLoopGroup](http://netty.io/4.0/api/io/netty/channel/nio/NioEventLoopGroup.html) will be used. The first one, often called 'boss', accepts an incoming connection. The second one, often called 'worker', handles the traffic of the accepted connection once the boss accepts the connection and registers the accepted connection to the worker. How many Threads are used and how they are mapped to the created [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)s depends on the [EventLoopGroup](http://netty.io/4.0/api/io/netty/channel/EventLoopGroup.html) implementation and may be even configurable via a constructor.
2. [ServerBootstrap](http://netty.io/4.0/api/io/netty/bootstrap/ServerBootstrap.html) is a helper class that sets up a server. You can set up the server using a [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html) directly. However, please note that this is a tedious process, and you do not need to do that in most cases.
3. Here, we specify to use the [NioServerSocketChannel](http://netty.io/4.0/api/io/netty/channel/socket/nio/NioServerSocketChannel.html) class which is used to instantiate a new [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html) to accept incoming connections.
4. The handler specified here will always be evaluated by a newly accepted [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html). The [ChannelInitializer](http://netty.io/4.0/api/io/netty/channel/ChannelInitializer.html) is a special handler that is purposed to help a user configure a new [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html). It is most likely that you want to configure the [ChannelPipeline](http://netty.io/4.0/api/io/netty/channel/ChannelPipeline.html) of the new [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html) by adding some handlers such as DiscardServerHandler to implement your network application. As the application gets complicated, it is likely that you will add more handlers to the pipeline and extract this anonymous class into a top level class eventually.
5. You can also set the parameters which are specific to the Channel implementation. We are writing a TCP\/IP server, so we are allowed to set the socket options such as tcpNoDelay and keepAlive. Please refer to the apidocs of [ChannelOption](http://netty.io/4.0/api/io/netty/channel/ChannelOption.html) and the specific [ChannelConfig](http://netty.io/4.0/api/io/netty/channel/ChannelConfig.html) implementations to get an overview about the supported ChannelOptions.
6. Did you notice option\(\) and childOption\(\)? option\(\) is for the [NioServerSocketChannel](http://netty.io/4.0/api/io/netty/channel/socket/nio/NioServerSocketChannel.html) that accepts incoming connections. childOption\(\) is for the [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)s accepted by the parent [ServerChannel](http://netty.io/4.0/api/io/netty/channel/ServerChannel.html), which is [NioServerSocketChannel](http://netty.io/4.0/api/io/netty/channel/socket/nio/NioServerSocketChannel.html) in this case.
7. We are ready to go now. What's left is to bind to the port and to start the server. Here, we bind to the port 8080 of all NICs \(network interface cards\) in the machine. You can now call the bind\(\) method as many times as you want \(with different bind addresses.\)

1.[NioEventLoopGroup](http://netty.io/4.0/api/io/netty/channel/nio/NioEventLoopGroup.html)是一个处理I\/O操作的多线程的事件循环。Netty提供不同种类的传输的各种[EventLoopGroup](http://netty.io/4.0/api/io/netty/channel/EventLoopGroup.html)实现。这个例子中，我们实现了服务器端应用程序，因此将使用两[NioEventLoopGroup](http://netty.io/4.0/api/io/netty/channel/nio/NioEventLoopGroup.html)。第一个，经常被称为“boss”，接受一个传入的连接。第二个，通常被称为“worker”，一旦boss接受连接，worker处理所接受的连接的负载，并注册接受的连接到worker。多少线程的使用和它们是如何映射到创建[Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)s，取决于[EventLoopGroup](http://netty.io/4.0/api/io/netty/channel/EventLoopGroup.html)实现，甚至可通过构造函数来配置。

2.[ServerBootstrap](http://netty.io/4.0/api/io/netty/bootstrap/ServerBootstrap.html)是一个辅助类，建立了一个服务器。您可以直接使用[Channel建立](http://netty.io/4.0/api/io/netty/channel/Channel.html)服务器。然而，请注意，这是一个繁琐的过程，在大多数情况下你不需要这样做。

3.在这里，我们指定要使用[NioServerSocketChannel](http://netty.io/4.0/api/io/netty/channel/socket/nio/NioServerSocketChannel.html)类进行实例化一个新的[Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)来接受传入的连接。

4.这里指定的handler将永远由一个新接受的[Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)进行评估。[ChannelInitializer](http://netty.io/4.0/api/io/netty/channel/ChannelInitializer.html)是一种特殊的handler，旨在帮助用户配置一个新的[Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)。这像似你想配置新的[Channel的](http://netty.io/4.0/api/io/netty/channel/Channel.html)[ChannelPipeline，通过](http://netty.io/4.0/api/io/netty/channel/ChannelPipeline.html)添加一些handlers如DiscardServerHandler去实现你的网络应用程序。由于应用程序变得复杂，它很可能是您将添加更多的handlers到管道中，并最终将这个匿名类提取到一个顶级类中。

5.你还可以设置特定Channel实现的参数。我们写一个TCP \/ IP的服务器，所以我们允许设置套接字选项如tcpNoDelay和KeepAlive。请参阅[ChannelOption](http://netty.io/4.0/api/io/netty/channel/ChannelOption.html)的apidocs和具体[ChannelConfig](http://netty.io/4.0/api/io/netty/channel/ChannelConfig.html)实现去得到支持的ChannelOptions概述。

6.你有没有注意到option\(\)和childOption\(\)？option\(\)是接受传入连接的[NioServerSocketChannel](http://netty.io/4.0/api/io/netty/channel/socket/nio/NioServerSocketChannel.html)。childOption\(\)是[Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html)s的通过父[ServerChannel](http://netty.io/4.0/api/io/netty/channel/ServerChannel.html)接受，这是在这种情况下，[NioServerSocketChannel](http://netty.io/4.0/api/io/netty/channel/socket/nio/NioServerSocketChannel.html)。

7.我们现在准备好了。剩下的是绑定到端口并启动服务器。在这里，我们把机器所有的网卡绑定到端口8080（网络接口卡）。你现在可以任意调用bind（）方法（通过不同的绑定地址。）

......

### Writing an Echo Server

......

1. A [ChannelHandlerContext](http://netty.io/4.0/api/io/netty/channel/ChannelHandlerContext.html) object provides various operations that enable you to trigger various I\/O events and operations. Here, we invoke write\(Object\) to write the received message in verbatim. Please note that we did not release the received message unlike we did in the DISCARD example. It is because Netty releases it for you when it is written out to the wire.
2. ctx.write\(Object\) does not make the message written out to the wire. It is buffered internally, and then flushed out to the wire by ctx.flush\(\). Alternatively, you could call ctx.writeAndFlush\(msg\) for brevity.
3. 一个[ChannelHandlerContext](http://netty.io/4.0/api/io/netty/channel/ChannelHandlerContext.html)对象提供了各种操作可以触发各种I\/O事件和操作。在这里，我们调用write\(Object\)在逐字写收到的消息。请注意，我们没有释放收到的消息，不像我们在DISCARD的例子。这是因为当它被写出到wire时，Netty为你释放它。

4. ctx.write\(Object\)不把消息写出到wire。这是内部缓冲，然后ctx.flush\(\)刷新到wire中。或者，你可以简洁的调用ctx.writeAndFlush\(msg\)。

  ......


### Writing a Time Server

The protocol to implement in this section is the [TIME](http://tools.ietf.org/html/rfc868) protocol. It is different from the previous examples in that it sends a message, which contains a 32-bit integer, without receiving any requests and closes the connection once the message is sent. In this example, you will learn how to construct and send a message, and to close the connection on completion.

Because we are going to ignore any received data but to send a message as soon as a connection is established, we cannot use the channelRead\(\) method this time. Instead, we should override the channelActive\(\) method. The following is the implementation:

在本节中实现的协议是时间协议。它不同于以前的示例，它发送一个消息，包含一个32位的整数，而不接收任何请求，并一旦消息被发送则关闭连接。在这个示例中，您将学习如何构建和发送一个消息，并完全的关闭连接。

因为我们要忽略所有收到的数据，但一旦建立了连接就发送消息，我们不能在这个时候使用channelRead\(\)方法。相反，我们应该重写channelActive\(\)方法。以下是实现：

......

1. As explained, the channelActive\(\) method will be invoked when a connection is established and ready to generate traffic. Let's write a 32-bit integer that represents the current time in this method.

正如前面解释的，在连接建立和准备产生流量时channelActive\(\)方法将被调用。让我们写一个32位的整数，表示在该方法中的当前时间。

1. To send a new message, we need to allocate a new buffer which will contain the message. We are going to write a 32-bit integer, and therefore we need a [ByteBuf](http://netty.io/4.0/api/io/netty/buffer/ByteBuf.html) whose capacity is at least 4 bytes. Get the current [ByteBufAllocator](http://netty.io/4.0/api/io/netty/buffer/ByteBufAllocator.html) via ChannelHandlerContext.alloc\(\) and allocate a new buffer.

要发送一个新消息，我们需要分配一个新的缓冲区，该缓冲区将包含该消息。我们要写一个32位的整数，因此我们需要一个[ByteBuf](http://netty.io/4.0/api/io/netty/buffer/ByteBuf.html)其容量至少为4字节。通过ChannelHandlerContext.alloc\(\)获取当前[ByteBufAllocator](http://netty.io/4.0/api/io/netty/buffer/ByteBufAllocator.html)和分配新的缓冲区。

1. As usual, we write the constructed message.

  But wait, where's the flip? Didn't we used to call java.nio.ByteBuffer.flip\(\) before sending a message in NIO? ByteBuf does not have such a method because it has two pointers; one for read operations and the other for write operations. The writer index increases when you write something to a ByteBuf while the reader index does not change. The reader index and the writer index represents where the message starts and ends respectively.

  In contrast, NIO buffer does not provide a clean way to figure out where the message content starts and ends without calling the flip method. You will be in trouble when you forget to flip the buffer because nothing or incorrect data will be sent. Such an error does not happen in Netty because we have different pointer for different operation types. You will find it makes your life much easier as you get used to it -- a life without flipping out!

  Another point to note is that the ChannelHandlerContext.write\(\) \(and writeAndFlush\(\)\) method returns a [ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html). A [ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html) represents an I\/O operation which has not yet occurred. It means, any requested operation might not have been performed yet because all operations are asynchronous in Netty. For example, the following code might close the connection even before a message is sent:

  `Channel ch = ...;`

  `ch.writeAndFlush(message);`

  `ch.close();`


Therefore, you need to call the close\(\) method after the [ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html) is complete, which was returned by the write\(\) method, and it notifies its listeners when the write operation has been done. Please note that, close\(\) also might not close the connection immediately, and it returns a [ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html).

像往常一样，我们写构建的信息。

但是等待，flip在哪里？我们不是通常在NIO发送消息之前调用java.nio.ByteBuffer.flip\(\) ？ByteBuf没有这样的方法，因为它有两个指针；读操作和写操作。在你写消息到ByteBuf时，写索引上升而读索引不改变。读索引和写索引表示消息的开始和结束的位置。

与此相反，不通过调用flip方法，NiO缓冲不提供清晰的方式去找出消息内容的开始与结束。当你忘记了flip缓冲区，因为没有什么或不正确的数据将被发送，你会有麻烦。这样的错误在Netty中不会发生，因为不同的操作类型有不同的指针。你会发现它使你的生活变得更容易，因为你习惯了它-一个没有翻转的生活！

还有一点要注意的是，ChannelHandlerContext.write\(\) \(and writeAndFlush\(\)\)方法返回一个[ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html)。一个[ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html)代表一个尚未发生的I\/O操作。这意味着，在Netty中任何请求的操作可能尚未进行，因为所有操作都是异步的。例如，下面的代码可能在发送消息之前关闭连接：

因此，你需要在[ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html)完成后调用close\(\)方法，这是由write\(\)方法返回，并通知其listeners当写操作已经完成。请注意，close\(\) 也可能不会立即关闭连接，并返回一个[ChannelFuture](http://netty.io/4.0/api/io/netty/channel/ChannelFuture.html)。
   4. How do we get notified when a write request is finished then? This is as simple as adding a [ChannelFutureListener](http://netty.io/4.0/api/io/netty/channel/ChannelFutureListener.html) to the returned ChannelFuture. Here, we created a new anonymous [ChannelFutureListener](http://netty.io/4.0/api/io/netty/channel/ChannelFutureListener.html) which closes the Channel when the operation is done.

Alternatively, you could simplify the code using a pre-defined listener:

当一个写请求完成时，我们如何得到通知？简单的做法是加入[ChannelFutureListener到](http://netty.io/4.0/api/io/netty/channel/ChannelFutureListener.html)ChannelFuture。在这里，我们创建了一个新的匿名[ChannelFutureListener，它将](http://netty.io/4.0/api/io/netty/channel/ChannelFutureListener.html)关闭通道当操作完成。

或者，你可以使用一个预定义的侦听器来简化代码：

`f.addListener(ChannelFutureListener.CLOSE);`

......

### Writing a Time Client

Unlike DISCARD and ECHO servers, we need a client for the TIME protocol because a human cannot translate a 32-bit binary data into a date on a calendar. In this section, we discuss how to make sure the server works correctly and learn how to write a client with Netty.

The biggest and only difference between a server and a client in Netty is that different [Bootstrap](http://netty.io/4.0/api/io/netty/bootstrap/Bootstrap.html) and [Channel](http://netty.io/4.0/api/io/netty/channel/Channel.html) implementations are used. Please take a look at the following code:

