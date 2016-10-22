Welcome to the Docs

Docker provides a way to run applications securely isolated in a container, packaged with all its dependencies and libraries. Because your application can always be run with the environment it expects right in the build image, testing and deployment is simpler than ever, as your build will be fully portable and ready to run as designed in any environment. And because containers are lightweight and run without the extra load of a hypervisor, you can run many applications that all rely on different libraries and environments on a single kernel, each one never interfering with the other. This allows you to get more out of your hardware by shifting the “unit of scale” for your application from a virtual or physical machine, to a container instance.

Docker提供了一种方式使得应用程序在容器中安全隔离的运行，打包它所有的依赖和类库。因为您的应用程序可以在构建镜像时所预期的环境下始终运行，所以测试和部署比以往任何时候都更简单，因为您的构建将是完全便携，并准备在任何环境下运行。因为容器是轻量级并且运行时无须额外的虚拟机管理程序负载，您可以在单一的内核上运行许多应用程序依赖于不同的类库和环境，每一个不干扰其他人。这允许您通过将“扩展单位”从一个虚拟或物理机器转移到一个容器实例，以获得更多的硬件。

Typical Docker Platform Workflow

1.Get your code and its dependencies into Docker [containers](https://docs.docker.com/engine/getstarted/step_two/):

1. * [Write a Dockerfile](https://docs.docker.com/engine/getstarted/step_four/) that specifies the execution environment and pulls in your code.

  * If your app depends on external applications \(such as Redis, or MySQL\), simply [find them on a registry such as Docker Hub](https://docs.docker.com/docker-hub/repos/), and refer to them in [a Docker Compose file](https://docs.docker.com/compose/overview/), along with a reference to your application, so they’ll run simultaneously.

    Software providers also distribute paid software via the [Docker Store](https://store.docker.com/).

  * Build, then run your containers on a virtual host via [Docker Machine](https://docs.docker.com/machine/overview/) as you develop.



2.Configure [networking](https://docs.docker.com/engine/tutorials/networkingcontainers/) and [storage](https://docs.docker.com/engine/tutorials/dockervolumes/) for your solution, if needed.

3.Upload builds to a registry \([ours](https://docs.docker.com/engine/tutorials/dockerrepos/), [yours](https://docs.docker.com/docker-trusted-registry/), or your cloud provider’s\), to collaborate with your team.

4.If you’re gonna need to scale your solution across multiple hosts \(VMs or physical machines\), [plan for how you’ll set up your Swarm cluster](https://docs.docker.com/engine/swarm/key-concepts/) and [scale it to meet demand](https://docs.docker.com/engine/swarm/swarm-tutorial/).

Note: Use [Universal Control Plane](https://docs.docker.com/ucp/overview/) and you can manage your Swarm cluster using a friendly UI!

5.Finally, deploy to your preferred cloud provider \(or, for redundancy, multiple cloud providers\) with [Docker Cloud](https://docs.docker.com/docker-cloud/overview/). Or, use [Docker Datacenter](https://www.docker.com/products/docker-datacenter), and deploy to your own on-premise hardware.

主要Docker平台流程

1.将代码与它的依赖放入Docker容器：

* 写一个Dockerfile指定执行环境并放进你的代码中

* 如果你的app依赖于外部程序（例如Redis，或者MySql），简单的方式是从注册列表找到它例如Docker Hub，并关联它们到Docker组合文件中，通过程序引用，它们会一起同时运行。

  软件也通过Docker Store提供分布式付费软件

* 但你开发时，构建，然后通过Docker Machine在虚拟机上运行你的容器


2.如果你的方案需要的话，配置网络和存储

3.上传构建到注册列表上（我们的，你们的，或你云提供者的），与你的团队合作

