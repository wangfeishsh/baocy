https:\/\/github.com\/spring-cloud\/spring-cloud-sleuth

......

Spring Cloud Sleuth features:

* Adds trace and span ids to the Slf4J MDC, so you can extract all the logs from a given trace or span in a log aggregator.
* Provides an abstraction over common distributed tracing data models: traces, spans \(forming a DAG\), annotations, key-value annotations. Loosely based on HTrace, but Zipkin \(Dapper\) compatible.
* Instruments common ingress and egress points from Spring applications \(servlet filter, rest template, scheduled actions, message channels, zuul filters, feign client\).
* If spring-cloud-sleuth-zipkin is available then the app will generate and collect Zipkin-compatible traces via HTTP. By default it sends them to a Zipkin collector service on localhost \(port 9411\). Configure the location of the service using spring.zipkin.baseUrl.

添加trace和span的ids到slf4j MDC，所以你可以从日志聚合器中提取一个给定的trace or span所有日志。

提供了一个抽象常见的分布式跟踪数据模型：traces，spans（形成一个DAG），注释，注释的关键值。基于HTrace，但兼容Zipkin（Dapper）。

Spring应用程序常见的入口和出口点 \(servlet filter, rest template, scheduled actions, message channels, zuul filters, feign client\).

.......

**Terminology**

Spring Cloud Sleuth borrows [Dapper’s](http://research.google.com/pubs/pub36356.html) terminology.

**Span:** The basic unit of work. For example, sending an RPC is a new span, as is sending a response to an RPC. Span’s are identified by a unique 64-bit ID for the span and another 64-bit ID for the trace the span is a part of. Spans also have other data, such as descriptions, timestamped events, key-value annotations \(tags\), the ID of the span that caused them, and process ID’s \(normally IP address\).

Spans are started and stopped, and they keep track of their timing information. Once you create a span, you must stop it at some point in the future.

| Tip | The initial span that starts a trace is called a root span. The value of span id of that span is equal to trace id. |
| --- | --- |

**Trace:** A set of spans forming a tree-like structure. For example, if you are running a distributed big-data store, a trace might be formed by a put request.

**Annotation:** is used to record existence of an event in time. Some of the core annotations used to define the start and stop of a request are:

* **cs** - Client Sent - The client has made a request. This annotation depicts the start of the span.
* **sr** - Server Received - The server side got the request and will start processing it. If one subtracts the cs timestamp from this timestamp one will receive the network latency.
* **ss** - Server Sent - Annotated upon completion of request processing \(when the response got sent back to the client\). If one subtracts the sr timestamp from this timestamp one will receive the time needed by the server side to process the request.
* **cr** - Client Received - Signifies the end of the span. The client has successfully received the response from the server side. If one subtracts the cs timestamp from this timestamp one will receive the whole time needed by the client to receive the response from the server.

Visualization of what **Span** and **Trace** will look in a system together with the Zipkin annotations:

**Span：**基本工作单元，例如，调用一个RPC从请求到响应都是同一个span，span通过一个64位ID唯一标识，trace以另一个64位ID表示，span还有其他数据信息，比如摘要、时间戳事件、关键值注释\(tags\)、span的ID、以及进度ID\(通常是IP地址\)

span在不断的启动和停止，同时记录了时间信息，当你创建了一个span，你必须在未来的某个时刻停止它。

注意：初始的span开始一个trace叫做根span。span ID的值与trace Id的值一样。

**Trace：**一系列spans组成的一个树状结构，例如，如果你正在跑一个分布式大数据工程，你可能需要创建一个trace。

**Annotation：**用来及时记录一个事件的存在，一些核心annotations用来定义一个请求的开始和结束

* **cs** - Client Sent - 客户端发起一个请求，这个annotion描述了这个span的开始
* **sr** - Server Received - 服务端获得请求并准备开始处理它，如果将其sr减去cs时间戳便可得到网络延迟
* **ss** - Server Sent - 注解表明请求处理的完成\(当请求返回客户端\)，如果ss减去sr时间戳便可得到服务端需要的处理请求时间
* **cr** - Client Received - 表明span的结束，客户端成功接收到服务端的回复，如果cr减去cs时间戳便可得到客户端从服务端获取回复的所有所需时间

将Span和Trace在一个系统中使用Zipkin注解的过程图形化：

![](https://raw.githubusercontent.com/spring-cloud/spring-cloud-sleuth/master/docs/src/main/asciidoc/images/trace-id.png)

Each color of a note signifies a span \(7 spans - from **A** to **G**\). If you have such information in the note:

`Trace Id = X`

`Span Id = D`

`Client Sent`

That means that the current span has **Trace-Id** set to **X**, **Span-Id** set to **D**. It also has emitted **Client Sent** event.

This is how the visualization of the parent \/ child relationship of spans would look like:

![](https://raw.githubusercontent.com/spring-cloud/spring-cloud-sleuth/master/docs/src/main/asciidoc/images/parents.png)

