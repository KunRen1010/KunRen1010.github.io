---
title: 'Section 3: Building Custom Images Through Docker Server'
date: 2022-01-17 21:00:21
tags: [Tech,Study]
---
# Section 3: Building Custom Images Through Docker Server

## How to build an Image?

### What is a dockerfile?

![](https://kunren1010.github.io/post-images/docker-images/section-3/image.png)

Dockerfile is a configuration file used to define some behaviors.

### How to write a custom dockerfile?

![](https://kunren1010.github.io/post-images/docker-images/section-3/image_1.png)

This diagram shows the basic point that how to create a dockerfile.

Firstly we need a base image, and install some dependencies and softwares in our **FileSystem** and finally run a command to start up.

Here, we  create a redis-server Image:

![](https://kunren1010.github.io/post-images/docker-images/section-3/image_2.png)

## How does dockerfile work?

When we run **docker build .**  we can see some logs from docker CLI.

```Bash
➜  ~ docker build .
[+] Building 3.6s (7/7) FINISHED                                                
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 102B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/alpine:latest           2.2s
 => [auth] library/alpine:pull token for registry-1.docker.io              0.0s
 => [1/2] FROM docker.io/library/alpine@sha256:21a3deaa0d32a8057914f36584  0.0s
 => => resolve docker.io/library/alpine@sha256:21a3deaa0d32a8057914f36584  0.0s
 => => sha256:c059bfaa849c4d8e4aecaeb3a10c2d9b3d85f5165c6 1.47kB / 1.47kB  0.0s
 => => sha256:21a3deaa0d32a8057914f36584b5288d2e5ecc98438 1.64kB / 1.64kB  0.0s
 => => sha256:e7d88de73db3d3fd9b2d63aa7f447a10fd0220b7cbf3980 528B / 528B  0.0s
 => [2/2] RUN apk add --update redis                                       1.3s
 => exporting to image                                                     0.1s
 => => exporting layers                                                    0.1s
 => => writing image sha256:22a152753cd2aff7e8addf5ff18af03ff3207978212b3  0.0s

```


![](https://kunren1010.github.io/post-images/docker-images/section-3/image_3.png)

Every time, docker runs a one-line command in the Dockerfile, the docker server will create a contemporary container to run that command. After that, the docker server will get the image (FileSystem Snapshot) from the last step. And then create a contemporary container and run the next command...

### How to tag a Image

We can get a name for Image. Like:

```Bash
docker build -t redis .

# we can run the image by its name
➜  ~ docker run redis
1:C 09 Jan 2022 15:24:32.739 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 09 Jan 2022 15:24:32.740 # Redis version=6.2.6, bits=64, commit=b39e1241, modified=0, pid=1, just started
1:C 09 Jan 2022 15:24:32.740 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 09 Jan 2022 15:24:32.742 * monotonic clock: POSIX clock_gettime
1:M 09 Jan 2022 15:24:32.743 * Running mode=standalone, port=6379.
1:M 09 Jan 2022 15:24:32.743 # Server initialized
1:M 09 Jan 2022 15:24:32.743 * Ready to accept connections

```




