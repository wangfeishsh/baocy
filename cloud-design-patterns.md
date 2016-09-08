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

### Compensating Transaction Pattern补偿事务模式

Undo the work performed by a series of steps, which together define an eventually consistent operation, if one or more of the operations fails. Operations that follow the eventual consistency model are commonly found in cloud-hosted applications that implement complex business processes and workflows.

![](/assets/IC709573.png)

### Competing Consumers Pattern

Enable multiple concurrent consumers to process messages received on the same messaging channel. This pattern enables a system to process multiple messages concurrently to optimize throughput, to improve scalability and availability, and to balance the workload.

![](/assets/IC709526-2.png)

### Compute Resource Consolidation Pattern计算资源整合模式

Consolidate multiple tasks or operations into a single computational unit. This pattern can increase compute resource utilization, and reduce the costs and management overhead associated with performing compute processing in cloud-hosted applications.

![](/assets/IC709519.png)

### Command and Query Responsibility Segregation \(CQRS\) Pattern

Segregate operations that read data from operations that update data by using separate interfaces. This pattern can maximize performance, scalability, and security; support evolution of the system over time through higher flexibility; and prevent update commands from causing merge conflicts at the domain level.

