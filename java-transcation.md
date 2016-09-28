参考：http:\/\/www.infoq.com\/cn\/minibooks\/JTDS

### 事务模型

Java 中有三种可以 的事务模型,分别称作本地事务模型\(Local Transaction Model\),编程式事务模型 \(Programmatic Transaction Model\),和声明式事务模型\(Declarative Transaction Model\)。虽 然,在随后的章节将分别深入讲解,我想在这里还是有必要将它们分别概述一下。

所谓“本地事务模型”,得名于**事实上不是编程框架本身来管理事务,事务是交给本地资源管理器\(local resource manager\)来管理的**。资源管理器是用于通信的、事实上的数据源\(data source\)提供者。举例来说,对于数据库,资源管理器是通过数据库驱动和数据库管理系统 \(Database Management System,DBMS\)来实现的。对于 JMS,所谓资源管理器就是通过特 定的 JMS 提供者\(provider\)实现的队列\(queue\)或主题\(topic\)的连接工厂\(connection factory\)。经由本地事务模型,开发人员管理的是“连接\(connection\)”,而非 “事务”。DBMS 和 JMS 的提供者真正管理了本地事务。关于“本地事务模型”的细节将在第二章介绍。

“编程式事务模型”**利用了 Java 事务 API\(Java Transaction API, JTA\)及其底层事务服务实现的 能量以提供事务支持,突破了“本地事务模型”的种种限制**。通过编程式事务模型,开发人员 的编码对象是“事务”,而非“连接”。通过使用 javax.transaction.UserTransation 接口,开发人 员调用 begin\(\)方法开始一个事务,调用 commit\(\)或 rollback\(\)方法去终止这个事务。虽然通 常情况下不鼓励大量使用编程式事务,然而,在客户端发起的,对远程无状态会话 Bean \(Stateless Session Beans\)的 EJB 事务访问的场景下,它还是有可能用到的。我们将在第三 章仔细讨论这种事务模型。

“声明式事务模型”,在 EJB 的世界中也成为**容器托管的事务模型\(Container-Managed Transactions\)**,是本书通篇所主要聚焦的事务模型。在声明式事务模型的环境下,软件框架 或“容器”管理了事务的开始和结束\(或者提交,或者回滚\)。开发人员仅仅需要告诉软件框架,碰到应用异常时“去回滚事务”即可,对事务的配置都是通过 EJB 中的 XML 部署描述文件 \(例如 ejb-jar.xml\)或 Spring 中的 bean 定义文件\(例如 ApplicationContext.xml\)来完成的。 有关“声明式事务模型”的细节将在本书第四章讨论。

### ACID

ACID 是描述有关事 务的期望特性的字首缩写,这些特性分别是原子性\(Atomicity\),一致性\(Consistency\),独 立性\(Isolation\),和持久性\(Durability\)。

“原子性”的含义是,一个事务必须将它产生的所有更改作为一个单独的工作单元提交,或 者回滚。无论在本次事务中产生了多少数量的更改,所有的更改需被当作一个整体处理。原 子特性有时被称作逻辑工作单元\(LUW,Logical Unit of Work\)特性,或称单一工作单元\(SUW, Single Unit of Work\)特性。

“一致性”的含义是,在活动事务的处理过程中,数据库必须时刻要避免被置于不一致 \(inconsistent\)的状态。这意味着在事务期间,每次对数据库实施的插入、更新或删除操作 时,数据库的完整性约束\(integrity constraints\)都要得到保证——即使在事务还未被提交时 也必须如此。这个特性对开发人员带来了影响。例如,一致性原则隐式地表明,在事务处理 的过程中,您不能在未添加主表记录\(summary record\)的情况下,先去添加从表记录\(detail record\)。虽然某些数据库允许将约束检查延迟到提交时进行,一般而言,您不能在事务处 理期间破坏外关键字限制,即使您打算随后修正它也不行。

“独立性”指的是各个独立事务之间的交互程度方面。ACID 特性的遵循度,决定了本事务在怎 样的程度下保证自身的未提交更改不受访问同一块信息的其他事务的影响。独立性是由一致 性和并发度共同决定的。在独立性级别提高时,一致性将会更好,但并发程度将降低。我们 将在第四章对“独立性”稍作更深入的讨论。

“持久化特性”的含义是,当我们收到了一个事务成功提交的信息,我们便能得到这个事务完 成的保证了,同时系统对数据库或 JMS 对象产生了永久的更改,这样的更改不会因为系统 失败而丢失——无论是我们对服务器控制台泼啤酒,还是在服务器上玩网游造成的宕机。而 今很多主流数据库厂商采用的一些精巧的缓存策略有可能对修改的持久性造成问题,但基本 上来说,当我们收到提交成功的信息时,我们就能被确保我们的修改是恒久的而且不被丢失 了。

### **JTA **和 **JTS**

Java 应用开发人员并不需要知道 **Java 事务服务\(Java Transaction Service, JTS\)**的幕后细节, 便能够有效地管理事务。然而,无论您采用 EJB 还是 Spring 框架,了解 Java 对分布式事务 处理的限制是非常重要的。

无论使用何种框架,大多数企业 Java 应用使用 **Java 事务接口\(Java Transaction API,JTA\)**进 行事务管理。JTA 是开发人员用于事务管理的接口。与之对应,Java 事务服务\(JTS\),是被 大多数开源或商业应用服务器所使用的,实现了 JTA 的底层事务服务\(注意市场上也有 JTS 之外的其他事务服务软件,能够用于某些应用服务器\)。我们可以认为 JTA 和 JTS 的关系与 JDBC 和对应的底层数据库驱动类似。JTA 可以通过商业中间件本身,或通过开源的事务管理 器,例如 JBoss 事务服务\(http:\/\/www.jboss.com\/products\/transactions\)、JOTM \(http:\/\/jotm.objectweb.org\)等实现。

Java 事务服务\(JTS\)是 CORBA 中 OTS1.1 规范的 Java 版\(OTS 是 Object Transaction Service 的缩写\)。对于一般开发人员,这并不特别重要——除非您具有打破砂锅问到底的嗜好,或 者要对付一次变态的面试。虽然 JTS 不是 J2EE 规范所要求必须实现的,但对于异构系统实现 间的分布式事务交互来说,JTS 是必须的。因为按照规范,JTA 必须同时支持 JTS 和非 JTS 的 实现,通常很难简单通过查看 JTA 接口的实现程度区分其支持的具体功能。例如,虽然 JTS 规范规定了对嵌套事务\(nest transaction\)的可选支持,J2EE 并不支持这一特性。您并不能 通过审查 JTA 体会到这个区别。幸运的是,在处理事务时,并没有很多接口是开发人员需要 真正关心的。例如,当使用编程式事务时,我们仅仅需要使用的唯一接口是 javax.transaction.UserTransaction。这个接口让我们能够显式的开始一个事务,提交一个事务, 回滚一个事务,并获取事务状态。当在 EJB 环境中使用声明式事务时,我们主要关心 EJBContext 接口中的 setRollbackOnly\(\)方法。我们诚然可以利用 javax.transaction 包中提供的 接口做很多事情,但开发人员实际上也不会怎么用到它们。例如我们将会在本书的后续章节 看到的一些直接访问事务管理器\(transaction manager\)的情况。这些情形发生在我们使用 声明式事务,然而需要手动暂停或继续事务的时候。我们当然也能够使用 TransactionManager 接口发起、提交或回滚一个特定的事务。

参考1：

大体上所来，JTA是一种接口规范，定义了一套接口，接口定义了相关的程序角色之间需要遵守的约定，JTA中约定了几种主要的程序角色，分别是事务管理器、事务客户、应用服务器、资源管理器。简单地说来就是，事务客户发起事务，应用服务器根据事物客户的请求决定如何向事务管理器体提交一个事务请求，如新建一个（require new），参与（require）等，事务管理器接收到了应用服务器发出的事务请求，然后事务管理器根据某种特定的协议（一般是两阶段提交协议）和资源管理器通过交换事务上下文来实现事务的功能。在这里，事务客户使用UserTransaction接口，应用服务器使用TransactionManager、Transaction、Synchronization接口，资源管理器实现XAResource接口。

那么JTS呢？JTA和JTS的关系如何呢？上面的内容有提到事务管理器要和资源管理器要进行事务上下文传播的交互，其中应用服务器和事务管理器之间也有传播事务上下文的交互，有时候事务客户和应用服务器也需要传播事务上下文，众所周知，只要涉及到软件交互往往都会有一套规范。那么如何来传递这种事务上下文呢？这就是JTS了。JTS也定义了一套规范，它约定了各个程序角色之间如何传递事务上下文，它源自CORBA 的OTS规范，基于IIOP（一种软件交互协议）。不要认为JTS是JTA的实现，JTA其实就定义了一个空架子，告诉JTA的实现者应该怎样做怎样做，但是具体到做的时候JTS就来插一手了。因为JTA约定的这些角色要进行事务上下文的交互啊，JTS约定了应该怎样去进行交互。

总体上来说JTA更多的是从框架的角度来约定程序角色的接口，而JTS则是从具体实现的角度来约定程序角色之间的接口，两者各司其职。照这样看来，SUN搞出来的这两个名字还真有点艺术感，有点哲学的味道了。

参考2:

**JTA**

JTA定义了一套接口，其中约定了几种主要的角色：TransactionManager、UserTransaction、Transaction、XAResource，并定义了这些角色之间需要遵守的规范，如Transaction的委托给TransactionManager等。

** JTS**

JTS也是一组规范，上面提到JTA中需要角色之间的交互，那应该如何交互？JTS就是约定了交互细节的规范。

总体上来说JTA更多的是从框架的角度来约定程序角色的接口，而JTS则是从具体实现的角度来约定程序角色之间的接口，两者各司其职。

因为JTA相对来说，更高层一些，我们主要关注JTA。

** XA**

XA协议，规定事务管理器和资源管理器接口，采用二阶段提交协议。

** 源码**

规范的接口位于javax.transaction：

![](http://img.blog.csdn.net/20140810110328712?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlkYXRnYg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### **UserTransaction **接口

UserTransaction 接口仅仅用于编程式事务模型,而且主要在 EJB 中使用。编程人员仅仅需要 关心其中的如下方法:

 begin\(\)

 commit\(\)

 rollback\(\)

 getStatus\(\)

**javax.transaction.UserTransaction.begin\(\)**

在编程式事务模型中,begin\(\)方法用于开启一个新的事务,并且将此事务与当前线程相关联。 如果某个事务已经与当前线程建立过关联,并且底层事务服务不支持嵌套事务,该方法会抛 出一个 NotSupportedException 异常。

**javax.transaction.UserTransaction.commit\(\)**

在编程式事务模型中,commit\(\)方法用于提交和当前线程关联的事务,并且终止该事务。这 个方法同时将此事务与当前线程解关联。在 Java 中,仅仅有一个事务能够与当前线程建立 关联。在 XA 环境下,这个方法可能抛出 HeuristicMixedException 或 HeuristicRollbackException, 表示资源管理器做出了独立于事务管理器的决定,在两阶段提交过程中回滚或者部分提交了 该事务。我们将在第五章对两阶段提交和“经验异常”处理做更深入的讨论。

**javax.transaction.UserTransaction.rollback\(\)**

在编程式事务模型中,rollback\(\)方法用于回滚和当前线程关联的事务,并终止该事务。这个 方法同时将此事务与当前线程解关联。

**javax.transaction.UserTransaction.getStatus\(\)**

在编程式事务模型中,getStatus\(\)方法返回一个整型数值,用以表示当前事务的状态。这个 整型返回值初看是没有什么意义的,不过我们可以使用 javax.transaction.Status 接口来确定 getStatus\(\)方法返回的值是什么含义。关于 javax.transaction.Status 接口的细节将在本章稍后 的部分讨论。

### **TransactionManager 接口**

javax.transaction.TransactionManager 接口主要用于声明式事务模型。在编程式事务下,使用 TransactionManager 接口,您能够做到使用 UserTransaction 接口基本上同样多的事情。然而, 对大多数方法而言,最好使用 UserTransaction,别去碰 TransactonManager 接口,除非您需 要暂停\(suspend\)或继续\(resume\)一个事务。

**javax.transaction.TransactionManager.suspend\(\)**

在声明式或编程式事务模型中,suspend\(\)方法用于暂停关联于当前线程的事务。该方法返回 当前事务的引用;如果没有事务和当前线程关联,则返回 null。在我们需要暂时停止当前事 务,执行一些不兼容 XA 的代码或存储过程时,这个方法是相当有用的。我们将会在本书的 第五章中看到有关这个方法的例子。

**javax.transaction.TransactionManager.resume\(\)**

在声明式或编程式事务模型中,resume\(\)方法用于继续之前暂停的事务。该方法的传入参数 为之前暂停的事务 \*,将此事务和当前线程关联,随即继续此事务。

### **EJBContext **接口

EJBContext 接口使用在 EJB 环境下的声明式事务模型中,对于事务管理,仅仅一个方法有用,

这就是 setRollbackOnly\(\)。 **javax.ejb.EJBContext.setRollbackOnly\(\)**

在声明式事务模型下,setRollbackOnly\(\)方法用于通知容器:当前事务仅可能产生的后果便 是回滚。有意思的是这个方法本身在被调用到的时候并不真正立即回滚事务;它只是标记事 务是“需要回滚”的,如果调用 getStatus\(\)方法,此时会返回 STATUS\_MARKED\_ROLLBACK 状态。 使用 TransactionManager.setRollbackOnly\(\)也会产生同样的结果。不过我们既已习惯使用了 SessionContext 或 MessageDrivenContext 等上下文对象,我们自然会使用\(EJBContext 提供的\) 这个方法。

**Status **接口

如前所述,我们能够通过 javax.transaction.Status 接口枚举值获取到事务的状态,这个值是 经由调用 UserTransaction.getStatus\(\)返回得到的。我花专门的一节来阐述这个问题,因为它 非常酷,它给我们提供了关于当前事务状态非常丰富的有用信息。Status 接口包括下面一些 枚举值:

 STATUS\_ACTIVE

 STATUS\_COMMITTED

 STATUS\_COMMITTING

 STATUS\_MARKED\_ROLLBACK

 STATUS\_NO\_TRANSACTION

 STATUS\_PREPARED

 STATUS\_PREPARING

 STATUS\_ROLLEDBACK

 STATUS\_ROLLING\_BACK

 STATUS\_UNKNOWN

在以上列出的所有状态值中,对主流 Java 业务应用开发人员真正有用的是这几个: STATUS\_ACTIVE,STATUS\_MARKED\_ROLLBACK 和 STATUS\_NO\_TRANSACTION。下面将会更详细 地究其细节和用途加以叙述。

**STATUS\_ACTIVE**

有时候,看看当前事务是否已经和线程关联的状态是重要的。例如,出于调试或优化的目的, 我们也许需要在查询的过程中加入一个拦截器或切面,查看事务是否存在。使用这个切面和 状态值,我们能够侦测到当前事务设计策略可能的待优化点。另一种情况下,我们如果需要 暂停当前事务,或执行可能造成应用失败的代码\(例如在 XA 环境下执行带有 DDL 的存储过 程\),我们也会去检查这个状态。下面的代码片段展示了 STATUS\_ACTIVE 状态的使用:

`... if (txn.getStatus() == Status.STATUS_ACTIVE)`

`logger.info("在查询操作中事务是活动的");`

`...`

此例的逻辑是,“如果我们在查询方法中发现有事务存在,则在日志文件中记录”。这可能意 味着我们虽然已具有事务,但实际上并不需要它。据此,可识别出一个可能的优化机会,或 暴露出我们事务设计整体策略中的问题。

**STATUS\_MARKED\_ROLLBACK**

在声明式事务模型下,这个状态常常是有用的。为了性能优化的需要,我们常常想要对之前 方法调用中被标记为回滚的事务进行跳过处理。因此,如果我们想要查看此事务是否被标记 回滚了,我们可以这样安排代码:

`...`

`if (txn.getStatus() == Status.STATUS_MARKED_ROLLBACK)`

`throw new Exception( "后续处理因事务回滚而终止");`

`...`

**STATUS\_NO\_TRANSACTION**

这个状态非常重要,因为它是确定是否真的没有事务上下文的唯一途径。和 STATUS\_ACTIVE 类似,它也可用来检查事务的存在与否,以便调试或优化。使用有关的切面或拦截器\(或内 联式代码\),我们能够监测到事务设计策略可能的漏洞,以便修改。下面例子代码说明了 STATUS\_NO\_TRANSACTION 的用法:

`...`

`if (txn.getStatus() == Status.STATUS_NO_TRANSACTION) throw new Exception(`

`"Transaction needed but none exists");`

`...`

注意我们不能简单地检查状态是否等于 STATUS\_ACTIVE 来确定事务上下文的存在,因为状态 不等于 STATUS\_ACTIVE 并不意味着事务上下文不存在——事务上下文也可能处于以上列表中 的任何其他状态。

总结

后续三章将聚焦于三种不同的事务模型。本书主要使用 EJB 作为例子来阐述这些概念,我也 提供了一些 Spring 的例子。虽然不同的持久性框架设计到的所有完整的例子不再本书涵盖范 围之内,我强烈建议读者通过阅读本书去理解有关这些事务模型的各种概念、技巧、陷阱和 最佳实践,而后查阅您所专注的持久化框架的文档,去进一步了解实现细节

## 第二章 本地事务模型

“本地事务”这个术语指的是这样一个事实:事务被底层数据库\(DBMS\)或在 JMS 中被底层 消息服务提供者所管理。从开发人员的角度来看,在本地事务模型中,我们所管理的并非“事 务”,而是“连接”。下面的代码展示了直接使用 JDBC 编码来进行本地事务模型管理的实例:

`public void updateTradeOrder(TradeOrderData order) throws Exception {`

`DataSource ds = (DataSource)`

`(new InitialContext()).lookup("jdbc/MasterDS");`

`Connection conn = ds.getConnection();`

`conn.setAutoCommit(false);`

`Statement stmt = conn.createStatement();`

`String sql = "update trade_order ... ";`

`try {`

`stmt.executeUpdate(sql);`

`conn.commit();`

`} catch (Exception e) {`

`conn.rollback();`

`throw e;`

`} finally {`

`stmt.close();`

`conn.close();`

`}`

`}`

注意在上面的例子中,我们对 Connection.setAutoCommit\(false\)和 Connection.commit\(\)、 Connection.rollback\(\)等方法的结合使用。setAutoCommit\(\)方法是开发者控制的连接管理场景 中非常重要的部分。自动提交标志\(auto commit flag\)告诉底层数据库,在每个 SQL 语句执 行完毕后,是否应该立即提交连接。true 值让数据库在执行了每个 SQL 语句后立即提交或回 滚本连接,而 false 值将导致连接仍然处于活动状态,不被提交,直至一个显式的 commit\(\) 方法调用。一般来说,这个标志默认设置为 true。因此,如果我们有多条 update 的 SQL 语 句,每条语句将被单独执行,单独提交,彼此独立,在这样的情况下对 Connection.commit\(\) 和 Connection.rollback\(\)的调用将被忽略。

在 Spring 框架中,可以简单地以如下方式使用 org.springframework.jdbc.datasource.DataSourceUtils 接口进行针对 JDBC 的底层进行编码:

`public void updateTradeOrder(TradeOrderData order) throws Exception {`

`Connection conn = DataSourceUtils.getConnection(dataSource);`

`conn.setAutoCommit(false);`

`Statement stmt = conn.createStatement(); String sql = "update trade_order ... ";`

`try {`

`stmt.executeUpdate(sql);`

`conn.commit();`

`} catch (Exception e) {`

`conn.rollback();`

`throw e; } finally {`

`stmt.close();`

`conn.close(); }`

`}`

在 Spring 环境下,数据源以及对应的业务逻辑对象可以在 Spring 配置文件中定义,举例如 下:

`<bean id="datasource" class="org.springframework.jndi.JndiObjectFactoryBean">`

`<property name="jndiName" value="jdbc/MasterDS"/> </bean>`

`<bean id="TradingService"`

`class="com.trading.server.TradingService"> <property name="dataSource">`

`<ref local="datasource"/> </property>`

`</bean>`

