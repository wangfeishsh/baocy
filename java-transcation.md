参考：http:\/\/www.infoq.com\/cn\/minibooks\/JTDS

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

### 

