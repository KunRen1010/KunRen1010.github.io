---
title: 'Section 8-9: Building and Dockerizeing a Multi-Container Application'
date: 2022-01-18 10:14:53
tags: [Tech,Study]
---
# Section 8-9: Building and Dockerizeing a Multi-Container Application

In the last section, we have built the whole workflow for a simple React application. But it is so simple and we didn't learn much knowledge how to combine different parts of a system. 

So in this section we will construct a system, like this:

![](https://kunren1010.github.io/post-images/docker-images/section-8-9/image.png)

In the real development, we need dev dockerfile that is a little different from the dockerfile in production.

You can download the whole system codes Here.

[131-checkpoint.zip](static/131-checkpoint.zip)

### Build container for each component.

First, for the **client**:

> Dockerfile.dev


```YAML
FROM node:alpine
WORKDIR /usr/app
COPY ./package.json .
RUN npm install
COPY . .
CMD ["npm","start"]
```


Second, for the **server**:

Dockerfile.dev

```YAML
FROM node:alpine
WORKDIR /usr/app
COPY ./package.json .
RUN npm install
COPY . .
CMD ["npm","run","dev"]
```


Third, for the worker:

Dockerfile.dev

```YAML
FROM node:alpine
WORKDIR /usr/app
COPY ./package.json .
RUN npm install
COPY . .
CMD ["npm","run","dev"]
```


### Write a docker-compose config file 

After finishing to build all the containers for each components. What is the next is that use docker-compose to combine them together.Here is our reqiurements:

![](https://kunren1010.github.io/post-images/docker-images/section-8-9/image_1.png)

> docker-compose.yml


```YAML
version: '3'
services:
  postgres:
    image: postgres
  redis:
    image: redis
  server:
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres
      - PGDATABASE=postgres
      - PGPASSWORD=postgres_password
      - PGPORT=5432
  client:
    stdin_open: true
    build:
      dockerfile: Dockerfile.dev
      context: ./client
    volumes:
      - /app/node_modules
      - ./client:/app
  worker:
    build:
      dockerfile: Dockerfile.dev
      context: ./woker
    volumes:
      - /app/node_modules
      - ./woker:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
```


That is it, if you are curios about *environment* in the config. please see this picture:

![](https://kunren1010.github.io/post-images/docker-images/section-8-9/image_2.png)

Yes, we can add our environment variables through these two ways.

But what's next? 

we need a http server like Nginx to serve our client.

![](https://kunren1010.github.io/post-images/docker-images/section-8-9/image_3.png)

![](https://kunren1010.github.io/post-images/docker-images/section-8-9/image_4.png)

> default.conf


```YAML
upstream client {
  server client:3000;
}

upstream api {
  server api:5000;
}

server {
  listen 80;

  location / {
    proxy_pass http://client;
  }

  location /api {
    rewrite /api/(.*) /$1 break;
    proxy_pass http://api;
  }
}
```


and nginx dockerfile:

> Dockerfile.dev


```YAML
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf

```


> docker-compose.yml


```YAML
version: '3'
services:
  postgres:
    image: 'postgres:latest'
    environment:
      - POSTGRES_PASSWORD=postgres_password
  redis:
    image: 'redis:latest'
  nginx:
    depends_on:
      - api
      - client
    restart: always
    build:
      dockerfile: Dockerfile.dev
      context: ./nginx
    ports:
      - '3050:80'
  api:
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres
      - PGHOST=postgres
      - PGDATABASE=postgres
      - PGPASSWORD=postgres_password
      - PGPORT=5432
  client:
    stdin_open: true
    build:
      dockerfile: Dockerfile.dev
      context: ./client
    volumes:
      - /app/node_modules
      - ./client:/app
  worker:
    build:
      dockerfile: Dockerfile.dev
      context: ./worker
    volumes:
      - /app/node_modules
      - ./worker:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
```


