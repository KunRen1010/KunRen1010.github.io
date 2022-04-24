---
title: 'Section 1: Dive Into Docker'
date: 2022-01-08 02:01:41
tags: [Tech,Study]
---
# Section 1: Dive Into Docker

When we start to learn some tech, The first thing is to figure out what is it and why we want to use it.

## What is docker?
![](https://kunren1010.github.io/post-images/docker-images/section-1/image.png)

## Why use docker?

In my opinion, there are two reason why we want to use it. And there are also lots of other benefits to use it. 

### 1. Easy to install and manage any environment and software

The traditional setup of a software is like this:

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_1.png)

So it is really complex to install a software especially for example, when there is python 3 on our machine, and we also want to install python 2. It is a little difficult to achieve that.

But docker has a strong ecosystem that provide lots of **Images** which can be installed in different isolated  C**ontainer**s, and we can use those images to build any software and dependencies.

### 2. Provide a simple and clean virtual environment

![](https://kunren1010.github.io/post-images/docker-images/section-1/docker-explained-3.png)

As we can see, when we run an application on a virtual machine like VMware or Virtualbox. We also need to install a basic operating system on that virtual machine. It cost a lot of resource like CPU, memory. But In the docker ecosystem we that just run that application with a small and basic image to cut down that extra cost.

## What is Image and Container?

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_2.png)

Image is a single file that remain some configs to establish a container in a virtual environment. So we can say "Container is a running instance of an image".

As I mentioned, it is difficult to use both python 2 and python 3 at the same time. Here is the problem.

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_3.png)

Chrome needs Python v2 but NodeJs needs Python v3. 

We can use **Namespacing** and **Control Groups** functions of Linux to distinguish these two versions like this:

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_4.png)

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_5.png)

**Image** is actually a FileSystem Snapshot with a startup command. And **container** is a running process which is provided some RAM, CPU and Network resources. 

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_6.png)

And docker is running in a virtual machine which contain a Linux Kernel. Which at the top of physic machine.

![](https://kunren1010.github.io/post-images/docker-images/section-1/image_7.png)



