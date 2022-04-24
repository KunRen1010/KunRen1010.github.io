---
title: 'Section 5: Docker Compose with Multiple Local Containers'
date: 2022-01-18 10:04:47
tags: [Tech,Study]
---
# Section 5: Docker Compose with Multiple Local Containers

## Small NodeJS application

In this section, we are going to create a local application like below:

![](https://kunren1010.github.io/post-images/docker-images/section-5/%E6%88%AA%E5%B1%8F2022-01-10%2010.02.40.png)

Here is the code:

> package.json


```JSON
{
  "dependencies": {
    "express": "*",
    "redis": "2.8.0"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```


> index.js


```JavaScript
const express = require('express');
const redis = require('redis');

const app = express();
const client = redis.createClient(6379,'127.0.0.1');
client.set('visits', 0);

app.get('/', (req, res) => {
  client.get('visits', (err, visits) => {a
    res.send('Number of visits is ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('Listening on port 8081');
});
```


So. baesed on this application we can write a Dockerfile:

> Dockerfile


```YAML
FROM node:alpine
WORKDIR /usr/app
COPY ./package.json .
RUN npm install
COPY . .
CMD ["npm","start"]
```


But when we run the image. It seems that the application cannot find redis server.

```Bash
➜  ~ docker run -p 8081:8081 eb66478b6b21

> start
> node index.js

Listening on port 8081
node:events:368
      throw er; // Unhandled 'error' event
      ^

Error: connect ECONNREFUSED 127.0.0.1:6379
    at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1157:16)
Emitted 'error' event on RedisClient instance at:
    at RedisClient.on_error (/usr/app/node_modules/redis/index.js:406:14)
    at Socket.<anonymous> (/usr/app/node_modules/redis/index.js:279:14)
    at Socket.emit (node:events:390:28)
    at emitErrorNT (node:internal/streams/destroy:164:8)
    at emitErrorCloseNT (node:internal/streams/destroy:129:3)
    at processTicksAndRejections (node:internal/process/task_queues:83:21) {
  errno: -111,
  code: 'ECONNREFUSED',
  syscall: 'connect',
  address: '127.0.0.1',
  port: 6379
}

```


## How to get a running redis server

We have two ways to get a connected redis server for the application.

![](https://kunren1010.github.io/post-images/docker-images/section-5/%E6%88%AA%E5%B1%8F2022-01-10%2010.23.44.png)

For the first method, you can find the solution in this blog:

[https://medium.com/geekculture/using-redis-with-docker-and-nodejs-express-71dccd495fd3](https://medium.com/geekculture/using-redis-with-docker-and-nodejs-express-71dccd495fd3)

## Docker Compose

For the second, We need to use Docker Compose. So what is Docker Compose?

![](https://kunren1010.github.io/post-images/docker-images/section-5/%E6%88%AA%E5%B1%8F2022-01-10%2010.26.39.png)

We can easily think that Docker Compose is just a tool to manage multiple Docker containers.

So how to  create a docker-compose.yml and how to use it?

![](https://kunren1010.github.io/post-images/docker-images/section-5/%E6%88%AA%E5%B1%8F2022-01-10%2010.14.16.png)

Here is a process that we want docker compose to do for us. Based on this process we can write a docker compose file, like:

> docker-compose.yml


```YAML
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    build: .
    ports:
      - '8081:8081'

```


 In this way, docker compose will help us create two separate containers. But how *node-app* get connected to *redis*? 

### Docker Compose Network

To do this, we need change our node-app code where redis client want to connect redis. 

> index.js


```YAML
const express = require('express');
const redis = require('redis');

const app = express();
const client = redis.createClient({
  host: 'redis-server',
  port: 6379,
});
client.set('visits', 0);

app.get('/', (req, res) => {
  client.get('visits', (err, visits) => {
    res.send('Number of visits ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('listening on port 8081');
});
```


**redis-server** is from *docker-compose.yml* (line 3). This is the hostname of the redis container. What is hostname? The same as *localhost* is our local machine  hostname. Which refer to*127.0.0.1* or any other local IP address.

Docker Compose commands

#### 1. docker-compose up

Which is used to run images through *docker-compose.yml*

#### 2. docker-compose up —build

—build flag means that rebuild all the containers.

#### 3.  docker-compose down

Used to shut down all the containers.

#### 4. docker-compose ps

Used to show the containers belong to this *docker-compose.yml*

