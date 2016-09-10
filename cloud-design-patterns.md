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

![](/assets/IC709572-2.png)

### Federated Identity Pattern  联合身份模式

Delegate authentication to an external identity provider. This pattern can simplify development, minimize the requirement for user administration, and improve the user experience of the application.

![](/assets/IC709540.png)

### Gatekeeper Pattern看门人模式

Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes净化 requests, and passes requests and data between them. This pattern can provide an additional layer of security, and limit the attack surface of the system.

![](/assets/IC709552.png)

### Health Endpoint Monitoring Pattern

Implement functional checks within an application that external tools can access through exposed endpoints at regular intervals. This pattern can help to verify that applications and services are performing correctly.

![](/assets/IC709539.png)

### Index Table Pattern

Create indexes over the fields in data stores that are frequently referenced by query criteria. This pattern can improve query performance by allowing applications to more quickly retrieve data from a data store.

![](/assets/IC709544.png)

### Leader Election Pattern

Coordinate the actions performed by a collection of collaborating task instances in a distributed application by electing one instance as the leader that assumes responsibility for managing the other instances. This pattern can help to ensure that tasks do not conflict with each other, cause contention for shared resources, or inadvertently interfere with the work that other task instances are performing.

![](/assets/IC707854.png)

### Materialized View Pattern

Generate pre-populated views over the data in one or more data stores when the data is formatted in a way that does not favor the required query operations. This pattern can help to support efficient querying and data extraction, and improve application performance.

![](/assets/IC709528.png)

### Pipes and Filters Pattern

Decompose a task that performs complex processing into a series of discrete elements that can be reused. This pattern can improve performance, scalability, and reusability by allowing task elements that perform the processing to be deployed and scaled independently.

![](/assets/IC707852.png)

![](/assets/IC707853.png)

### Priority Queue Pattern

Prioritize requests sent to services so that requests with a higher priority are received and processed more quickly than those of a lower priority. This pattern is useful in applications that offer different service level guarantees to individual types of client.

![](/assets/IC709554.png)

![](/assets/IC709555.png)

### Queue-based Load Leveling Pattern

Use a queue that acts as a buffer between a task and a service that it invokes in order to smooth intermittent heavy loads that may otherwise cause the service to fail or the task to timeout. This pattern can help to minimize the impact of peaks in demand on availability and responsiveness for both the task and the service.

![](/assets/IC709531-2.png)

### Retry Pattern

Enable an application to handle temporary failures when connecting to a service or network resource by transparently retrying the operation in the expectation that the failure is transient. This pattern can improve the stability of the application.

![](/assets/IC709538.png)

### Runtime Reconfiguration Pattern

Design an application so that it can be reconfigured without requiring redeployment or restarting the application. This helps to maintain availability and minimize downtime.

![](/assets/IC709533.png)

### Scheduler Agent Supervisor Pattern

Coordinate a set of actions across a distributed set of services and other remote resources, attempt to transparently handle faults if any of these actions fail, or undo the effects of the work performed if the system cannot recover from a fault. This pattern can add resiliency to a distributed system by enabling it to recover and retry actions that fail due to transient exceptions, long-lasting faults, and process failures.

![](/assets/IC709520.png)

### Sharding Pattern

Divide a data store into a set of horizontal partitions shards. This pattern can improve scalability when storing and accessing large volumes of data.

### Static Content Hosting Pattern静态内容托管模式

Deploy static content to a cloud-based storage service that can deliver these directly to the client. This pattern can reduce the requirement for potentially expensive compute instances.

### Throttling Pattern节流模式

Control the consumption of resources used by an instance of an application, an individual tenant, or an entire service. This pattern can allow the system to continue to function and meet service level agreements, even when an increase in demand places an extreme load on resources.

### Valet Key Pattern备用钥匙模式

Use a token or key that provides clients with restricted direct access to a specific resource or service in order to offload data transfer operations from the application code. This pattern is particularly useful in applications that use cloud-hosted storage systems or queues, and can minimize cost and maximize scalability and performance.

