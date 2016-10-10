http:\/\/www.javaworld.com\/article\/2077963\/open-source-tools\/distributed-transactions-in-spring--with-and-without-xa.html

**How to Distributed transactions in Spring, with and without XA**

**Seven transaction-processing patterns for Spring applications**

While it's common to use the Java Transaction API and the XA protocol for distributed transactions in Spring, you do have other options. The optimum implementation depends on the types of resources your application uses and the trade-offs you're willing to make between performance, safety, reliability, and data integrity. In this JavaWorld feature, SpringSource's David Syer guides you through seven patterns for distributed transactions in Spring applications, three of them with XA and four without. _Level: Intermediate_

在Spring中使用java事务API和分布式事务XA协议是很常见的，当然你也有其他的选择。最佳的实现取决于应用程序使用的资源类型和您愿意在性能、安全性、可靠性和数据完整性之间进行权衡。在JavaWorld特征，SpringSource的David Syer指导你通过在Spring应用分布式事务的七种模式，其中三个使用XA而四个没有。级别：中级

The Spring Framework's support for the Java Transaction API \(JTA\) enables applications to use distributed transactions and the XA protocol [without running in a Java EE container](http://www.javaworld.com/javaworld/jw-04-2007/jw-04-xa.html). Even with this support, however, XA is expensive and can be unreliable or cumbersome to administrate. It may come as a welcome surprise, then, that a certain class of applications can avoid the use of XA altogether.

Spring框架支持java事务API（JTA）可以使用分布式事务和XA协议在没有java EE容器中运行。即使有这样的支持，然而，XA是昂贵的，也可能是不可靠的或笨重的管理。它可能会成为一个受欢迎的惊喜，那么，这一类的应用程序可以完全避免使用XA。

To help you understand the considerations involved in various approaches to distributed transactions, I'll analyze seven transaction-processing patterns, providing code samples to make them concrete. I'll present the patterns in reverse order of safety or reliability, starting with those with the highest guarantee of data integrity and atomicity under the most general circumstances. As you move down the list, more caveats and limitations will apply. The patterns are also roughly in reverse order of runtime cost \(starting with the most expensive\). The patterns are all architectural, or technical, as opposed to business patterns, so I don't focus on the business use case, only on the minimal amount of code to see each pattern working.

为了帮助您了解分布式事务所涉及的各种方法的考虑，我将分析七个事务处理模式，提供代码示例，使它们成为具体的。我会倒序展示这些模式，在安全性或可靠性方面，从那些具有最高保证数据的完整性和原子性的最普通的情况下开始。当你移动到列表，更多的警告和限制将适用。模式也大致在运行时成本的相反顺序（从最昂贵的开始）。模式是所有的架构，或技术，而不是商业模式，所以我不专注于业务用例，只有在最小数量的代码，看看每一个模式的工作。

Note that only the first three patterns involve XA, and those might not be available or acceptable on performance grounds. I don't discuss the XA patterns as extensively as the others because they are covered elsewhere, though I do provide a simple demonstration of the first one. By reading this article you'll learn what you can and can't do with distributed transactions and how and when to avoid the use of XA -- and when not to.

请注意，只有前三个模式涉及XA，这些模式可能由于性能的理由变的不可用或不可接受。我不讨论XA模式和其他一些模式一样广泛，因为他们在其他地方，虽然我也提供一个简单的第一个示范。通过阅读这篇文章，你会了解你使用分布式事务能做什么和不能做，以及何时如何避免使用XA的--当不。

**Distributed transactions and atomicity**

A _distributed transaction_ is one that involves more than one transactional resource. Examples of transactional resources are the connectors for communicating with relational databases and messaging middleware. Often such a resource has an API that looks something like begin\(\), rollback\(\), commit\(\). In the Java world, a transactional resource usually shows up as the product of a factory provided by the underlying platform: for a database, it's a Connection \(produced by DataSource\) or [Java Persistence API](http://www.javaworld.com/javaworld/jw-01-2008/jw-01-jpa1.html) \(JPA\) EntityManager; for [Java Message Service](http://www.javaworld.com/jw-01-1999/jw-01-jms.html) \(JMS\), it's a Session.

分布式事务是一个涉及多个事务资源的事务。事务性资源的例子是用于与关系数据库和消息中间件进行通信的连接器。通常这些资源有一个API，看上去有点像begin\(\)，rollback\(\)，commit\(\)。在java的世界，一个事务资源通常显示为一个工厂的底层平台提供的产品：一个数据库，它是一个连接（由数据源）或java持久化API（JPA EntityManager）；对于java消息服务（JMS），这是一个会话。

In a typical example, a JMS message triggers a database update. Broken down into a timeline, a successful interaction goes something like this:

在一个典型的例子，一个JMS消息触发数据库更新。分解成一个时间线，一个成功的交互，像这样的：

1. Start messaging transaction
2. **Receive message**
3. Start database transaction
4. **Update database**
5. Commit database transaction
6. Commit messaging transaction

If a database error such as a constraint violation occurred on the update, the desirable sequence would look like this:

如果更新时发生的数据库错误，如约束冲突，则期望的后果将是这样的：

1. Start messaging transaction
2. **Receive message**
3. Start database transaction
4. **Update database, fail!**
5. Roll back database transaction
6. Roll back messaging transaction

In this case, the message goes back to the middleware after the last rollback and returns at some point to be received in another transaction. This is usually a good thing, because otherwise you might have no record that a failure occurred. \(Mechanisms to deal with automatic retry and handling exceptions are out of this article's scope.\)

在这种情况下，消息在最后一次回滚后返回到中间件，并在另一个事务中接收到某个点上的返回。这通常是一件好事，因为否则你可能没有记录发生故障。（处理自动重试和处理异常的机制是在这篇文章的范围。）

The important feature of both timelines is that they are _atomic_, forming a single logical transaction that either succeeds completely or fails completely.

这两个时间线的重要特征是，它们是原子的，形成一个单一的逻辑事务，要么成功完全或完全失败。

But what guarantees that the timeline looks like either of these sequences? Some synchronization between the transactional resources must occur, so that if one commits they both do, and vice versa. Otherwise, the whole transaction is not atomic. The transaction is distributed because multiple resources are involved, and without synchronization it will not be atomic. The technical and conceptual difficulties with distributed transactions all relate to the synchronization of the resources \(or lack of it\).

但是，什么保证时间线看起来像这些结果中的任一个？事务资源之间的一些同步必须发生，所以如果一个提交他们都这样做，反之亦然。否则，整个交易不是原子性的。事务是分布式的，因为涉及到多个资源，并且没有同步，它不会被原子化的。分布式事务的技术和概念上的困难都涉及到资源的同步（或缺乏）。

The first three patterns discussed below are based on the XA protocol. Because these patterns have been widely covered, I won't go into much detail about them here. Those familiar with XA patterns may want to skip ahead to the [Shared Transaction Resource pattern](http://www.javaworld.com/article/2077963/open-source-tools/distributed-transactions-in-spring--with-and-without-xa.html?page=3#strp).

下面的前三种模式讨论是基于XA协议。因为这些模式已被广泛覆盖，我不会在这里很详细地谈论他们。那些熟悉XA模式可能想跳过前面到事务资源共享模式。

**Full XA with 2PC**

If you need close-to-bulletproof guarantees that your application's transactions will recover after an outage, including a server crash, then Full XA is your only choice. The shared resource that is used to synchronize the transaction in this case is a special transaction manager that coordinates information about the process using the XA protocol. In Java, from the developer's point of view, the protocol is exposed through a JTA UserTransaction.

如果你需要接近防弹保证应用程序的事务中断后恢复，包括服务器崩溃，那么Full XA是你唯一的选择。共享资源，用于在这种情况下，事务同步是一种特殊的事务管理，协调使用XA协议的过程信息。在java中，从开发者的角度来看，该协议通过JTA UserTransaction暴露。

Being a system interface, XA is an enabling technology that most developers never see. They need to know is that it's there, what it enables, what it costs, and the implications for how they use transactional resources. The cost comes from the [two-phase commit](http://www.javaworld.com/jw-07-2000/jw-0714-transaction.html) \(2PC\) protocol that the transaction manager uses to ensure that all resources agree on the outcome of a transaction before it ends.

作为一个系统接口，XA是一个有利的技术，大多数开发者不会看到。他们需要知道的是，它的存在，它可以做什么，它的成本，以及它们如何使用事务性资源的影响。成本来自于两阶段提交（2PC）协议，事务管理器以确保所有资源达成事务的结果在它结束之前。

If the application is Spring-enabled, it uses the Spring JtaTransactionManager and Spring declarative transaction management to hide the details of the underlying synchronization. The difference for the developer between using XA and not using XA is all about configuring the factory resources: the DataSource instances, and the transaction manager for the application. This article includes a sample application \(the atomikos-db project\) that illustrates this configuration. The DataSource instances and the transaction manager are the only XA- or JTA-specific elements of the application.

如果应用程序是Spring启用，它采用Spring JtaTransactionManager和Spring声明式事务管理隐藏底层的同步的细节。对于开发者来说，使用XA和不使用XA是所有关于配置工厂资源的区别：DataSource实例和应用程序的事务管理器。这篇文章包含了一个示例应用程序（atomikos DB项目），展示说明该配置。DataSource实例和事务管理器是唯一的XA -或JTA的特定应用的元素。

To see the sample working, run the unit tests under com.springsource.open.db. A simple MulipleDataSourceTests class just inserts data into two data sources and then uses the Spring integration support features to roll back the transaction, as shown in Listing 1:

**Listing 1. Transaction rollback**

`@Transactional`

`@Test`

`public void testInsertIntoTwoDataSources() throws Exception {`

`int count = getJdbcTemplate().update(`

`"INSERT into T_FOOS (id,name,foo_date) values (?,?,null)", 0,`

`"foo");`

`assertEquals(1, count);`

`count = getOtherJdbcTemplate()`

`.update(`

`"INSERT into T_AUDITS (id,operation,name,audit_date) values (?,?,?,?)",`

`0, "INSERT", "foo", new Date());`

`assertEquals(1, count);`

`// Changes will roll back after this method exits`

`}`

Then MulipleDataSourceTests verifies that the two operations were both rolled back, as shown in Listing 2:

**Listing 2. Verifying rollback**

`@AfterTransaction`

`public void checkPostConditions() {`

`int count = getJdbcTemplate().queryForInt("select count(*) from T_FOOS");`

`// This change was rolled back by the test framework`

`assertEquals(0, count);`

`count = getOtherJdbcTemplate().queryForInt("select count(*) from T_AUDITS");`

`// This rolled back as well because of the XA`

`assertEquals(0, count);`

`}`

For a better understanding of how Spring transaction management works and how to configure it generally, see the [Spring Reference Guide](http://static.springframework.org/spring/docs/2.5.x/reference/new-in-2.html#new-in-2-middle-tier).

**XA with 1PC Optimization**

This pattern is an optimization that many transaction managers use to avoid the overhead of 2PC if the transaction includes a single resource. You would expect your application server to be able to figure this out.

这种模式是一种优化，许多事务管理器使用以避免提交的开销，如果事务包括一个单一的资源。您希望您的应用程序服务器能够解决这个。

**XA and the Last Resource Gambit **策略

Another feature of many XA transaction managers is that they can still provide the same recovery guarantees when all but one resource is XA-capable as they can when they all are. They do this by ordering the resources and using the non-XA resource as a casting vote. If it fails to commit, then all the other resources can be rolled back. It is close to 100 percent bulletproof -- but is not quite that. And when it fails, it fails without leaving much of a trace unless extra steps are taken \(as is done in some of the top-end implementations\).

许多XA事务管理的另一个特点是，他们仍然可以提供相同的恢复保证当只有一个资源是XA能力他们能当他们都。他们这样做是通过有序的资源和使用非XA资源投票。如果它没有提交，那么所有其他资源可以回滚。这是接近百分之100防弹-但不是很。当它失败时，没有留下太多的痕迹，除非采取额外的步骤（如在一些高端实现）。

**Shared Transaction Resource pattern**

A great pattern for decreasing complexity and increasing throughput in some systems is to remove the need for XA altogether by ensuring that all the transactional resources in the system are actually backed by the same resource. This is clearly not possible in all processing use cases, but it is just as solid as XA and usually much faster. The Shared Transaction Resource pattern is bulletproof but specific to certain platforms and processing scenarios.

为了降低复杂性和提高吞吐量，在某些系统中一个好的模式是通过确保系统中所有事务性资源实际上都是由同一个资源支持完全免除对XA的需要。这显然是不可能在所有处理的用例，但它只是作为固体Xa和通常快很多。共享的事务资源模式是防弹的，但特定于平台和处理场景。

A simple and familiar \(to many\) example of this pattern is the sharing of a database Connection between a component that uses object-relational mapping \(ORM\) with a component that uses [JDBC](http://www.javaworld.com/javaworld/jw-05-2006/jw-0501-jdbc.html). This is what happens you use the Spring transaction managers that support the ORM tools such as [Hibernate](http://www.javaworld.com/javaworld/jw-10-2004/jw-1018-hibernate.html), [EclipseLink](http://www.eclipse.org/eclipselink/), and the [Java Persistence API](http://www.javaworld.com/javaworld/jw-01-2008/jw-01-jpa1.html) \(JPA\). The same transaction can safely be used across ORM and JDBC components, usually driven from above by a service-level method execution where the transaction is controlled.

一个简单的和熟悉的（许多）模式的例子是，在一个使用对象关系映射（ORM）的组件与一个使用JDBC的组件共享数据库连接。这就是你使用Spring的事务管理器，支持ORM工具如Hibernate，eclipselink，和java持久化API（JPA）。同样的事务可以安全地在ORM和JDBC组件的使用，通常是自上而下由服务级别的方法执行的事务控制。

Another effective use of this pattern is the case of message-driven update of a single database \(as in the simple example in this article's introduction\). Messaging-middleware systems need to store their data somewhere, often in a relational database. To implement this pattern, all that's needed is to point the messaging system at the same database the business data is going into. This pattern relies on the messaging-middleware vendor exposing the details of its storage strategy so that it can be configured to point to the same database and hook into the same transaction.

这种模式的另一种有效的使用是一个单一的数据库的消息驱动的更新的情况下（在这篇文章中的简单的例子中的介绍）。消息中间件系统需要在某个关系数据库中存储他们的数据。要实现这种模式，需要的是在同一个数据库中指向消息系统，业务数据正在进行中。这种模式依赖于消息中间件供应商公开其存储策略的详细信息，以便它可以被配置为指向同一个数据库，并将其挂钩到同一个事务中。

Not all vendors make this easy. An alternative, which works for almost any database, is to use [Apache ActiveMQ](http://activemq.apache.org/) for messaging and plug a storage strategy into the message broker. This is fairly easy to configure once you know the trick. It's demonstrated in this article's shared-jms-db samples project. The application code \(unit tests in this case\) does not need to be aware that this pattern is in use, because it is all enabled declaratively in Spring configuration.

A unit test in the sample called SynchronousMessageTriggerAndRollbackTests verifies that everything is working with synchronous message reception. The testReceiveMessageUpdateDatabase method receives two messages and uses them to insert two records in the database. When this method exits, the test framework rolls back the transaction, so you can verify that the messages and the database updates are both rolled back, as shown in Listing 3:

**Listing 3. Verifying rollback of messages and database updates**

`@AfterTransaction`

`public void checkPostConditions() {`

`assertEquals(0, SimpleJdbcTestUtils.countRowsInTable(jdbcTemplate, "T_FOOS"));`

`List<String> list = getMessages();`

`assertEquals(2, list.size());`

`}`

The most important features of the configuration are the ActiveMQ persistence strategy, linking the messaging system to the same DataSource as the business data, and the flag on the Spring JmsTemplate used to receive the messages. Listing 4 shows how to configure the ActiveMQ persistence strategy:

**Listing 4. Configuring ActiveMQ persistence**

`<bean id="connectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory"`

`depends-on="brokerService">`

`<property name="brokerURL" value="vm://localhost?async=false" />`

`</bean>`

`<bean id="brokerService" class="org.apache.activemq.broker.BrokerService" init-method="start"`

`destroy-method="stop">`

`...`

`<property name="persistenceAdapter">`

`<bean class="org.apache.activemq.store.jdbc.JDBCPersistenceAdapter">`

`<property name="dataSource">`

`<bean class="com.springsource.open.jms.JmsTransactionAwareDataSourceProxy">`

`<property name="targetDataSource" ref="dataSource"/>`

`<property name="jmsTemplate" ref="jmsTemplate"/>`

`</bean>`

`</property>`

`<property name="createTablesOnStartup" value="true" />`

`</bean>`

`</property>`

`</bean>`

Listing 5 shows the flag on the Spring JmsTemplate that is used to receive the messages:

**Listing 5. Setting up the **JmsTemplate** for transactional use**

`<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">`

`...`

`<!-- This is important... -->`

`<property name="sessionTransacted" value="true" />`

`</bean>`

Without sessionTransacted=true, the JMS session transaction API calls will never be made and the message reception cannot be rolled back. The important ingredients here are the embedded broker with a special async=false parameter and a wrapper for the DataSource that together ensure that ActiveMQ uses the same transactional JDBC Connection as Spring.

