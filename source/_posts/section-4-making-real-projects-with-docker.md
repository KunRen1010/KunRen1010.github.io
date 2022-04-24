---
title: 'Section 4: Making Real Projects with Docker'
date: 2022-01-18 03:02:40
tags: [Tech,Study]
---
# Section 4: Making Real Projects with Docker

## First, We create a NodeJS demo project, like this:

> package.json


```JSON
{
  "dependencies": {
    "express": "*"
  },
  "scripts": {
    "start": "node index.js"
  }
}

```


> index.js


```JavaScript
const express = require('express');

const app = express();

app.get('/', (req, res) => {
  res.send('Hi there');
});

app.listen(8080, () => {
  console.log('Listening on port 8080');
});
```


This is a simple project. And we can run this NodeJS project locally by using *npm install* and *npm run*.

![](https://kunren1010.github.io/post-images/docker-images/section-4/image.png)

So if we want to edit a Dockerfile to use docker to run this project. We can do some similar things when we create an image for redis.

![](https://kunren1010.github.io/post-images/docker-images/section-4/image_1.png)

But when we run Dockerfile like this:

> Dockerfile


```YAML
FROM alpine
RUN npm install
CMD ["npm", "start"]
```


```Bash
➜  simpleweb 2 docker build .
[+] Building 1.9s (6/6) FINISHED                                                
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 91B                                        0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/alpine:latest           1.6s
 => [auth] library/alpine:pull token for registry-1.docker.io              0.0s
 => CACHED [1/2] FROM docker.io/library/alpine@sha256:21a3deaa0d32a805791  0.0s
 => ERROR [2/2] RUN npm install                                            0.2s
------
 > [2/2] RUN npm install:
#6 0.191 /bin/sh: npm: not found
------
executor failed running [/bin/sh -c npm install]: exit code: 127
```


There is an error " npm not found". Why? Actually the reason is that the base image alpine does not contain *npm package manager*. So we need to change our base image to an image which contain *npm*, for example: *node:alpine*

> Dockerfile


```YAML
FROM node:alpine
RUN npm install
CMD ["npm", "start"]
```


But there is a new error:

```Bash
➜  simpleweb 2 docker build .
[+] Building 3.1s (6/6) FINISHED                                                
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 96B                                        0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/node:alpine             1.4s
 => [auth] library/node:pull token for registry-1.docker.io                0.0s
 => CACHED [1/2] FROM docker.io/library/node:alpine@sha256:4dd690ef859cea  0.0s
 => ERROR [2/2] RUN npm install                                            1.6s
------                                                                          
 > [2/2] RUN npm install:
#6 1.553 npm ERR! Tracker "idealTree" already exists
#6 1.556 
#6 1.556 npm ERR! A complete log of this run can be found in:
#6 1.556 npm ERR!     /root/.npm/_logs/2022-01-09T19_18_59_392Z-debug-0.log
------
executor failed running [/bin/sh -c npm install]: exit code: 1
```


It said that "failed running", when we searched this error in google, we found that npm didn't find package.json file in the image work directory. So how to fix it? yes, just copy our local files into image before we run *npm install*. And we would also *specific a work directory *for this application. Otherwise, this error will occur.

> Dockerfile


```YAML
FROM node:alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]

```


Yes, now, It work!

```Bash
➜  simpleweb 2 docker build .
[+] Building 4.2s (9/9) FINISHED                                                
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 118B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/node:alpine             0.6s
 => [internal] load build context                                          0.0s
 => => transferring context: 434B                                          0.0s
 => [1/4] FROM docker.io/library/node:alpine@sha256:4dd690ef859ceadc242e9  0.0s
 => CACHED [2/4] WORKDIR /app                                              0.0s
 => [3/4] COPY . .                                                         0.0s
 => [4/4] RUN npm install                                                  3.3s
 => exporting to image                                                     0.2s
 => => exporting layers                                                    0.2s
 => => writing image sha256:ea974380f73c49623b7e2b65507a404640befae7b6372  0.0s 
```


## Next, run this image and open a port

![](https://kunren1010.github.io/post-images/docker-images/section-4/image_2.png)

By default, we cannot get access to docker container. So we need open a port for the application when we run this image.

![](https://kunren1010.github.io/post-images/docker-images/section-4/image_3.png)

```Bash
# docker run -p <Local Port> : <Container Port> <image id>
➜  simpleweb 2 docker run -p 3000:8080 ea974380f73c

> start
> node index.js

Listening on port 8080

```


![](https://kunren1010.github.io/post-images/docker-images/section-4/%E6%88%AA%E5%B1%8F2022-01-09%2021.35.50.png)

Now, it works good so far.

## Minimizing Cache Busting and Rebuilds

When we make any change on our application. We need to rebuild our Dockerfile. But here is a problem. 

```Bash
➜  simpleweb 2 docker build .
[+] Building 1.7s (10/10) FINISHED                                              
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 36B                                        0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/node:alpine             1.6s
 => [auth] library/node:pull token for registry-1.docker.io                0.0s
 => [internal] load build context                                          0.0s
 => => transferring context: 352B                                          0.0s
** => [1/4] FROM docker.io/library/node:alpine@sha256:4dd690ef859ceadc242e9  0.0s
 => CACHED [2/4] WORKDIR /app                                              0.0s
 => CACHED [3/4] COPY . .                                                  0.0s
 => CACHED [4/4] RUN npm install                                           0.0s**
 => exporting to image                                                     0.0s
 => => exporting layers                                                    0.0s
 => => writing image sha256:ea974380f73c49623b7e2b65507a404640befae7b6372  0.0s
```


We can see that if we do not change the Dockerfile, docker will rebuild the image from cache. But if we change the application code. what will happen?\

```Bash
➜  simpleweb 2 vim index.js 
➜  simpleweb 2 docker build .
[+] Building 4.1s (9/9) FINISHED                                                 
 => [internal] load build definition from Dockerfile                        0.0s
 => => transferring dockerfile: 36B                                         0.0s
 => [internal] load .dockerignore                                           0.0s
 => => transferring context: 2B                                             0.0s
 => [internal] load metadata for docker.io/library/node:alpine              0.6s
 => [1/4] FROM docker.io/library/node:alpine@sha256:4dd690ef859ceadc242e99  0.0s
 => [internal] load build context                                           0.0s
 => => transferring context: 556B                                           0.0s
 => CACHED [2/4] WORKDIR /app                                               0.0s
 => [3/4] COPY . .                                                          0.0s
 => [4/4] RUN npm install                                                   3.3s
 => exporting to image                                                      0.2s
 => => exporting layers                                                     0.2s
 => => writing image sha256:d5358f17ecaef0757294a7569d212ba28d0c90dc474f66  0.0s

```


We can find that **docker rebuild from the line we modified**, but that makes sense, because the FileSystem snapshot from the that line has changed. We do not have a cache from that modified line.

So how can we optimize our Dockerfile to make *npm install* not run again and again? But to use the cache?

> Dockerfile


```YAML
FROM node:alpine
WORKDIR /usr/app
COPY ./package.json .
RUN npm install
COPY . .
CMD ["npm", "start"]
```


```Bash
➜  simpleweb 2 vim index.js 
➜  simpleweb 2 docker build .
[+] Building 0.7s (10/10) FINISHED                                              
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 36B                                        0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/node:alpine             0.6s
 => [1/5] FROM docker.io/library/node:alpine@sha256:4dd690ef859ceadc242e9  0.0s
 => [internal] load build context                                          0.0s
 => => transferring context: 553B                                          0.0s
 => CACHED [2/5] WORKDIR /usr/app                                          0.0s
 => CACHED [3/5] COPY ./package.json .                                     0.0s
 => CACHED [4/5] RUN npm install                                           0.0s
 => [5/5] COPY . .                                                         0.0s
 => exporting to image                                                     0.0s
 => => exporting layers                                                    0.0s
 => => writing image sha256:2b65a064f4ee33dc9ebbb786647c860a3ce2b4a41f266  0.0s
```


