参考：http:\/\/ifeve.com\/netty-in-action\/

Netty系列之Netty高性能之道 http:\/\/www.infoq.com\/cn\/articles\/netty-high-performance

Preface

The Problem

Nowadays we use general purpose applications or libraries to communicate with each other. For example, we often use an HTTP client library to retrieve information from a web server and to invoke a remote procedure call via web services. However, a general purpose protocol or its implementation sometimes does not scale very well. It is like we don't use a general purpose HTTP server to exchange huge files, e-mail messages, and near-realtime messages such as financial information and multiplayer game data. What's required is a highly optimized protocol implementation which is dedicated to a special purpose. For example, you might want to implement an HTTP server which is optimized for AJAX-based chat application, media streaming, or large file transfer. You could even want to design and implement a whole new protocol which is precisely tailored to your need. Another inevitable case is when you have to deal with a legacy proprietary protocol to ensure the interoperability with an old system. What matters in this case is how quickly we can implement that protocol while not sacrificing the stability and performance of the resulting application.

前言

问题

现在，我们使用应用程序或库的一般目的是相互通信。例如，我们经常使用HTTP客户端库从Web服务器检索信息和调用远程过程调用通过Web服务。然而，一个通用的协议或它的实现有时可扩展性不是很好。这就像我们不使用通用的HTTP服务器来交换大文件，电子邮件，和近实时信息，如财务信息和游戏数据。所需要的是一个高度优化的协议实现，这是专门为一个特殊的目的。例如，你可能想实现一个HTTP服务器，它是优化基于AJAX的聊天应用，流媒体，或大文件传输。你甚至可以设计和实现一个新的协议，这正是根据你的需要精确定制。另一个不可避免的情况是当你必须处理一个遗留的专有协议，以确保与旧系统的互操作性。在这种情况下，重要的是我们如何快速地实现该协议，同时不牺牲所得到的应用程序的稳定性和性能。

The Solution

[_The Netty project_](http://netty.io/) is an effort to provide an asynchronous event-driven network application framework and tooling for the rapid development of maintainable high-performance · high-scalability protocol servers and clients.

In other words, Netty is an NIO client server framework which enables quick and easy development of network applications such as protocol servers and clients. It greatly simplifies and streamlines network programming such as TCP and UDP socket server development.

'Quick and easy' does not mean that a resulting application will suffer from a maintainability or a performance issue. Netty has been designed carefully with the experiences earned from the implementation of a lot of protocols such as FTP, SMTP, HTTP, and various binary and text-based legacy protocols. As a result, Netty has succeeded to find a way to achieve ease of development, performance, stability, and flexibility without a compromise.

Some users might already have found other network application framework that claims to have the same advantage, and you might want to ask what makes Netty so different from them. The answer is the philosophy it is built on. Netty is designed to give you the most comfortable experience both in terms of the API and the implementation from the day one. It is not something tangible but you will realize that this philosophy will make your life much easier as you read this guide and play with Netty.

解决方案

[_The Netty project_](http://netty.io/) 致力于提供一个异步事件驱动网络应用框架，它为快速发展的服务器和客户端之间提供可维护高性能·高扩展性协议工具。

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

2.[ServerBootstrap](http://netty.io/4.0/api/io/netty/bootstrap/ServerBootstrap.html)是一个辅助类，建立了一个服务器。您可以直接使用通道设置服务器。然而，请注意，这是一个繁琐的过程，你不需要这样做，在大多数情况下。

在这里，我们指定要使用的nioserversocketchannel类进行实例化一个新的渠道来接受传入的连接。

这里指定的处理程序将永远由一个新接受的信道进行评估。是一种特殊的channelinitializer处理器，旨在帮助用户配置一个新的通道。这是最有可能的，你想添加一些程序如discardserverhandler实施你的网络应用程序配置的新通道ChannelPipeline。由于应用程序变得复杂，它很可能是您将添加更多的处理程序到管道中，并最终将这个匿名类提取到一个顶级类中。

还可以设置特定于信道实现的参数。我们写一个TCP \/ IP的服务器，所以我们允许设置套接字选项如tcpnodelay和KeepAlive。请参阅channeloption的apidocs和具体channelconfig实现得到支持的channeloptions概述。

你有没有注意到option\(\)和childoption\(\)？option\(\)是接受传入连接的nioserversocketchannel。childoption\(\)是频道的母serverchannel接受，这是在这种情况下，nioserversocketchannel。

我们现在准备好了。剩下的是绑定到端口并启动服务器。在这里，我们所有的网卡绑定到端口8080（网络接口卡）在机。你现在可以叫（）方法很多你想要的东西（不同的绑定地址。）

