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

