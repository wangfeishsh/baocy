参考：http:\/\/ifeve.com\/netty-in-action\/

Netty系列之Netty高性能之道 http:\/\/www.infoq.com\/cn\/articles\/netty-high-performance

Preface

The Problem

Nowadays we use general purpose applications or libraries to communicate with each other. For example, we often use an HTTP client library to retrieve information from a web server and to invoke a remote procedure call via web services. However, a general purpose protocol or its implementation sometimes does not scale very well. It is like we don't use a general purpose HTTP server to exchange huge files, e-mail messages, and near-realtime messages such as financial information and multiplayer game data. What's required is a highly optimized protocol implementation which is dedicated to a special purpose. For example, you might want to implement an HTTP server which is optimized for AJAX-based chat application, media streaming, or large file transfer. You could even want to design and implement a whole new protocol which is precisely tailored to your need. Another inevitable case is when you have to deal with a legacy proprietary protocol to ensure the interoperability with an old system. What matters in this case is how quickly we can implement that protocol while not sacrificing the stability and performance of the resulting application.

前言

问题

现在，我们使用应用程序或库的一般目的是相互通信。例如，我们经常使用HTTP客户端库从Web服务器检索信息和调用远程过程调用通过Web服务。然而，一个通用的协议或它的实现有时可扩展性不是很好。这就像我们不使用通用的HTTP服务器来交换大文件，电子邮件，和近实时信息，如财务信息和游戏数据。所需要的是一个高度优化的协议实现，这是专门为一个特殊的目的。例如，你可能想实现一个HTTP服务器，它是优化基于AJAX的聊天应用，流媒体，或大文件传输。你甚至可以设计和实现一个新的协议，这正是根据你的需要。另一个不可避免的情况是当你必须处理一个传统的专有协议，以确保与旧系统的互操作性。在这种情况下，重要的是我们如何快速地实现该协议，同时不牺牲所得到的应用程序的稳定性和性能。

