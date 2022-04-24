---
title: 'Section 2: Manipulating Containers with the Docker Client'
date: 2022-01-17 06:14:08
tags: [Tech,Study]
---
# Section 2: Manipulating Containers with the Docker Client

## Run a container 

### How to create and run a container from an image?

We can use this command to create a container

```Bash
# docker run <image name>
docker run hello-world
```


As I mentioned before, image consist of *a FileSystem Snapshot* and a *Startup Command*. When running **docker run** docker server will create a container where there is *the same FlieSystem Snapshot,* and run the *startup command*, Like this diagram:

![](https://kunren1010.github.io/post-images/docker-images/section-2/image.png)

We can **override** the startup command by adding more command after *docker run*.

When I run the command that diagram shows on my own machine, we can see the output below:

```Bash
# docker run image <override command>
➜  ~ docker run busybox ls
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
5cc84ad355aa: Pull complete 
Digest: sha256:5acba83a746c7608ed544dc1533b87c737a0b0fb730301639a0179f9344b1678
Status: Downloaded newer image for busybox:latest
bin
dev
etc
home
proc
root
sys
tmp
usr
var
```


## More useful commands

### List container

```Bash
# list running container
➜  ~ docker ps
CONTAINER ID   IMAGE     COMMAND             CREATED         STATUS         PORTS     NAMES
5394a283723c   busybox   "ping google.com"   7 seconds ago   Up 7 seconds             crazy_ramanujan

# list all container
➜  ~ docker ps -a
CONTAINER ID   IMAGE         COMMAND             CREATED          STATUS                      PORTS     NAMES
5394a283723c   busybox       "ping google.com"   53 seconds ago   Up 52 seconds                         crazy_ramanujan
59e6b66bbcd5   busybox       "ls"                7 minutes ago    Exited (0) 7 minutes ago              wonderful_rosalind
fbcf6dcb378d   hello-world   "/hello"            14 minutes ago   Exited (0) 14 minutes ago             wizardly_dubinsky

```


### Get logs from a container

```Bash
# docker logs <container id>
➜  ~ docker logs 5394a283723c 
PING google.com (142.250.74.142): 56 data bytes
64 bytes from 142.250.74.142: seq=0 ttl=37 time=22.207 ms
64 bytes from 142.250.74.142: seq=1 ttl=37 time=21.690 ms
64 bytes from 142.250.74.142: seq=2 ttl=37 time=21.698 ms
64 bytes from 142.250.74.142: seq=3 ttl=37 time=21.766 ms
```


### Stop and kill a container

Stop a container means that shut down the process by normal method. Just like closing an application, we need to wait for the process to save their contemporary data and close network establishing.

Killing a container doesn't care anything, but just kill the process right now by force. 

```Bash
# docker stop <container id>
➜  ~ docker stop faf774b5ed5f
# wait 5 secondes if container do not close, then stop the container forcely
faf774b5ed5f

# docker stop <container id>
➜  ~ docker kill e9cf53ebce4b
# kill the process intermediately
e9cf53ebce4b
```


**If there is no normal method to shut down the process, docker will wait 5 seconds and stop this container by force.**

Here are two diagram to show how docker stop and kill a container.

![](https://kunren1010.github.io/post-images/docker-images/section-2/image_1.png)

![](https://kunren1010.github.io/post-images/docker-images/section-2/image_2.png)

### Go inside of a container 

We can use **-it** flag to go to inside of a container. The flag **-i** means **interactive** will instruct to pass the **STDIN** to the container. And the flag **-t** means **tty**  will create a tty which make the output format looks better.

```Bash
# docker exec -it <container id> <command>

➜  ~ docker exec -it 9c0dec78addb sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var

➜  ~ docker exec -it 9c0dec78addb ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var

```


#### By the way, What is **STDIN**?

![](https://kunren1010.github.io/post-images/docker-images/section-2/image_3.png)

Every container has own STDIN, STDOUT and STDERR for standard input and output stream. And **-i** flag is used to allow your typing in your own windows or macos terminal can pass into the container. 

