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

有些用户可能已经找到了其他的网络应用框架，声称有同样的优势，你可能想问是什么让Netty如此不同。答案是它是建立在哲学上的。Netty设计，从一开始在API和实现上都给你最舒适的体验。它不是有形的东西但你会认识到，这种哲学将使你的生活更容易，但你阅读本指南并使用Netty。

