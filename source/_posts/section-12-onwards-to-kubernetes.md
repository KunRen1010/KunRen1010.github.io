---
title: 'Section 12: Onwards to Kubernetes'
date: 2022-02-03 07:39:23
tags: [Tech,Study]
---

Before we learn a new tech, we want to find out what is it and why we need it. In order to explain these two questions, please see pictures below:

![](https://kunren1010.github.io/post-images/docker-images/section-12/image.png)

When we don't have Kubernetes, we can use docker-compose to scale our application. But it have a lot of  unnecessary components like nginx, client also be scaled up. Since the calculating unit is only the worker, we can just only scale worker component.

![](https://kunren1010.github.io/post-images/docker-images/section-12/image_1.png)

So Kubernetes can divide containers into different Nodes, and there is a Master to control all the Nodes.

![](https://kunren1010.github.io/post-images/docker-images/section-12/image_2.png)
