参考：http:\/\/www.infoq.com\/cn\/minibooks\/JTDS

Java 中有三种可以 的事务模型,分别称作本地事务模型\(Local Transaction Model\),编程式事务模型 \(Programmatic Transaction Model\),和声明式事务模型\(Declarative Transaction Model\)。虽 然,在随后的章节将分别深入讲解,我想在这里还是有必要将它们分别概述一下。

所谓“本地事务模型”,得名于**事实上不是编程框架本身来管理事务,事务是交给本地资源管 理器\(local resource manager\)来管理的**。资源管理器是用于通信的、事实上的数据源\(data source\)提供者。举例来说,对于数据库,资源管理器是通过数据库驱动和数据库管理系统 \(Database Management System,DBMS\)来实现的。对于 JMS,所谓资源管理器就是通过特 定的 JMS 提供者\(provider\)实现的队列\(queue\)或主题\(topic\)的连接工厂\(connection factory\)。经由本地事务模型,开发人员管理的是“连接\(connection\)”,而非 “事务”。DBMS 和 JMS 的提供者真正管理了本地事务。关于“本地事务模型”的细节将在第二章介绍。

“编程式事务模型”**利用了 Java 事务 API\(Java Transaction API, JTA\)及其底层事务服务实现的 能量以提供事务支持,突破了“本地事务模型”的种种限制**。通过编程式事务模型,开发人员 的编码对象是“事务”,而非“连接”。通过使用 javax.transaction.UserTransation 接口,开发人 员调用 begin\(\)方法开始一个事务,调用 commit\(\)或 rollback\(\)方法去终止这个事务。虽然通 常情况下不鼓励大量使用编程式事务,然而,在客户端发起的,对远程无状态会话 Bean \(Stateless Session Beans\)的 EJB 事务访问的场景下,它还是有可能用到的。我们将在第三 章仔细讨论这种事务模型。

“声明式事务模型”,在 EJB 的世界中也成为**容器托管的事务模型\(Container-Managed Transactions\)**,是本书通篇所主要聚焦的事务模型。在声明式事务模型的环境下,软件框架 或“容器”管理了事务的开始和结束\(或者提交,或者回滚\)。开发人员仅仅需要告诉软件框架,碰到应用异常时“去回滚事务”即可,对事务的配置都是通过 EJB 中的 XML 部署描述文件 \(例如 ejb-jar.xml\)或 Spring 中的 bean 定义文件\(例如 ApplicationContext.xml\)来完成的。 有关“声明式事务模型”的细节将在本书第四章讨论。

