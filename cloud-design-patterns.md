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

![](/assets/IC702504.png)

### Event Sourcing Pattern

Use an append-only store to record the full series of events that describe actions taken on data in a domain, rather than storing just the current state, so that the store can be used to materialize the domain objects. This pattern can simplify tasks in complex domains by avoiding the requirement to synchronize the data model and the business domain; improve performance, scalability, and responsiveness; provide consistency for transactional data; and maintain full audit trails and history that may enable compensating actions.

![](/assets/IC709550.png)

### External Configuration Store Pattern

Move configuration information out of the application deployment package to a centralized location. This pattern can provide opportunities for easier management and control of configuration data, and for sharing configuration data across applications and application instances.

### Federated Identity Pattern 联合身份模式

Delegate authentication to an external identity provider. This pattern can simplify development, minimize the requirement for user administration, and improve the user experience of the application.

### Gatekeeper Pattern看门人模式

Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes净化 requests, and passes requests and data between them. This pattern can provide an additional layer of security, and limit the attack surface of the system.

### Health Endpoint Monitoring Pattern

