1、Install

2、 Get Started with Docker

* install Docker software for your platform
* run a software image in a container
* browse for an image on Docker Hub
* create your own image and run it in a container
* create a Docker Hub account and an image repository
* create an image of your own
* push your image to Docker Hub for others to use

......

Docker Engine provides the core Docker technology that enables images and containers. As the last step in your installation, you ran the docker run hello-world command. The command you ran had three parts.

![](https://docs.docker.com/engine/getstarted/tutimg/container_explainer.png)

Build your own image

**Step 1: Write a Dockerfile**

In this step, you use your favorite text editor to write a short Dockerfile. A Dockerfile describes the software that is “baked” into an image. It isn’t just ingredients tho, it can tell the software what environment to use or what commands to run. Your recipe is going to be very short.

在这一步中，您可以使用您喜爱的文本编辑器来编写一个简短的Dockerfile。一个Dockerfile描述软件是如何“烘培”制成一个镜像。它不只是组成部份，它还可以告诉软件什么环境下使用或什么命令来运行。你的描述（食谱）应该会很短。

Dockerfile

`FROM docker/whalesay:latest`

`RUN apt-get -y update && apt-get install -y fortunes`

`CMD /usr/games/fortune -a | cowsay`

The FROM keyword tells Docker which image your image is based on. Whalesay is cute and has the cowsay program already, so we’ll start there.

The fortunes program has a command that prints out wise sayings for our whale to say. So, the first step is to install it. This line installs the software into the image.

This line tells the fortune program to pass a nifty quote to the cowsay program.

**Step 2: Build an image from your Dockerfile**

Now, build your new image by typing the docker build -t docker-whale . command in your terminal \(don’t forget the . period\).

**Step 3: Learn about the build process**

**Step 4: Run your new docker-whale**

Tag, push, and pull your image

Step 1: Tag and push the image

_Notice that currently, the \`REPOSITORY\` shows the repo name \`docker-whale\`_

_but not the namespace. You need to include the \`namespace\` _**\*\***_for_**\*\***_ Docker Hub to_

_associate it _**\*\***_with_**\*\***_ your account. The \`namespace\` is the same _**\*\***_as_**\*\***_ your Docker_

_Hub account name. You need to rename the image to_

_\`YOUR\_DOCKERHUB\_NAME\/docker-whale\`._

![](https://docs.docker.com/engine/getstarted/tutimg/tagger.png)

Step 2: Pull your new image

Use the docker rmi to remove the maryatdocker\/docker-whale and docker-whale images.

You can use an ID or the name to remove an image.

`$ docker rmi -f 7d9495d03763`

`$ docker rmi -f docker-whale`

Learning more

3、Learn by Example

`docker ps`

`docker logs`

`docker stop`

`docker version`

```docker ``run`` -d -P training/webapp python app.py```

Review what the command did. You’ve specified two flags: -d and -P. You’ve already seen the -d flag which tells Docker to run the container in the background. The -P flag is new and tells Docker to map any required network ports inside our container to our host. This lets us view our web application.

`PORTS`

`0.0.0.0:49155->5000/tcp`

When we passed the -P flag to the docker run command Docker mapped any ports exposed in our image to our host.

In this case Docker has exposed port 5000 \(the default Python Flask port\) on port 49155.

Network port bindings are very configurable in Docker. In our last example the -P flag is a shortcut for -p 5000 that maps port 5000 inside the container to a high port \(from ephemeral port range which typically ranges from 32768 to 61000\) on the local Docker host. We can also bind Docker containers to specific ports using the -p flag, for example:

`docker run -d -p 80:5000 training/webapp python app.py`

This would map port 5000 inside our container to port 80 on our local host. You might be asking about now: why wouldn’t we just want to always use 1:1 port mappings in Docker containers rather than mapping to high ports? Well 1:1 mappings have the constraint of only being able to map one of each port on your local host.

Suppose you want to test two Python applications: both bound to port 5000 inside their own containers. Without Docker’s port mapping you could only access one at a time on the Docker host.

So you can now browse to port 49155 in a web browser to see the application.

![](https://docs.docker.com/engine/tutorials/webapp1.png)

_Note: If you have been using a virtual machine on macOS, Windows or Linux, you’ll need to get the IP of the virtual host instead of using localhost. You can do this by running the docker-machine ip your\_vm\_name from your command line or terminal application, for example:_

`$ docker-machine ip my-docker-vm`

192.168.99.100

In this case you’d browse to http:\/\/192.168.99.100:49155 for the above example.

