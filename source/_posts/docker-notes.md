---
title: 'Docker Notes'
date: 2021-12-01 06:15:53
tags: [Tech,Study]
---
# Docker
Just some Notes of docker

### Docker flags
#### 1. -t (tty) 
will create a tty where you can interact with the container.
#### 2. -i (interactive) 
will instruct to pass the STDIN to the container. 
#### 3. -d (detached) 
the container will run in the background.

```shell
docker run -d -it --name looper ubuntu
```
**docker run -d** run container detached, **-it** allows you to interact with the container by using the command line.**--name looper** set a name to the container instead of a random name.

### 4. -f
we can see the logs with the command below
```shell
docker logs -f looper
```
you can also see the logs with "attach"
```shell
docker attach loopper
```
if you press control+c in the attached terminal, the container is stopped. So it's better to use **--no--stdin** flag for safity.

### Docker command
#### 1. exec
If you want to create a new terminal in the container, you can run 
```shell
docker exec -it looper bash
```
#### 2. kill
You can also kill the process like below:
```shell
docker kill looper
```
#### 3. rm
You can remove the container 
```shell
docker rm looper
```
#### 4. search
You can search images from the Docker Hub
```shell
docker search nginx
```
#### 5. tag
You can rename your image like:
```shell
docker tag ubuntu:18.04 ubuntu:bionic
```
#### 6. cp
```shell
docker cp /additional.txt zen_rosalind:/usr/src/app/
```

### Bind mount
```shell 
docker run -v "$(pwd):/mydir" youtube-dl https://imgur.com/JY5tHqr
```

### Port open
> To expose a port, add line EXPOSE <port> in your Dockerfile.
> To publish a port, run the container with -p <host-port>:<container-port>

### Notice
An image name may consist of 3 parts plus a tag. 
Usually like the following: **registry/organisation/image:tag**

**clean docker commandline**
> docker container prune
> docker system prune

### ENTRYPOINT VS CMD
What is the difference between **ENTRYPOINT** and **CMD**?
**ENTRYPOINT** is entry of the program, or just like a fuction.
**CMD** is just like default parameter of **ENTRYPOINT** (fuction). And we can easily override it by adding an argument of docker run command.  

### Shell form and exec form 
The main difference of those two is the bracket[] and quote "".
```shell 
Shell form: <instruction> <command>
Exec form: <instruction> ["executable", "parameter"]
```
The better way is only use exec form, because shell form will cause some unknown problem sometimes.

## Depoly image 



# Docker Compose
### Bind a volume 
Volumes in docker-compose are defined with the following syntax **location-in-host:location-in-container**
```yaml
    nginx:
      image: nginx:alpine
      volumes:
          - ./nginx.conf:/etc/nginx/nginx.conf:ro
      ports:
          - 80:80
      depends_on:
          - backend
          - frontend
      container_name: nginx
```

### Environment variables
```yaml
version: '3.5'

services:
  backend:
      image:  
      environment:
        - VARIABLE=VALUE
        - VARIABLE 
```
The version number is no less than 2, because otherwise the syntax is significantly different

### Network
A major benefit of defining network is that it makes it easy to setup a configuration where other containers connect to an existing network as an external network. 
```yaml
version: "3.8"

services:
  db:
    image: postgres:13.2-alpine
    networks:
      - database-network # Name in this docker-compose file

networks:
  database-network: # Name in this docker-compose file
    name: database-network # Name that will be the actual name of the network
```
To connect to an external network (possibly defined another docker-compose.yml):
```yaml
version: "3.8"

services:
  db:
    image: backend-image
    networks:
      - database-network

networks:
  database-network:
    external:
      name: database-network # Must match the actual name of the network
```
