来源：https:\/\/msdn.microsoft.com\/en-us\/library\/dn568099.aspx

云设计模式

云应用的规范性体系结构指南

### Cache-aside Pattern

Load data on demand into a cache from a data store. This pattern can improve performance and also helps to maintain consistency between data held in the cache and the data in the underlying data store.

![](/assets/IC709568.png)

### Circuit Breaker Pattern

Handle faults that may take a variable amount of time to rectify when connecting to a remote service or resource. This pattern can improve the stability and resiliency of an application.

当连接远程服务或资源时，花费少量的时间来纠正处理错误。可以提高引用的稳定性与弹性

![](/assets/IC709532.png)

Compensating Transaction Pattern

