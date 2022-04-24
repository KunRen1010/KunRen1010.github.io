---
title: 'Section 6: Creating a Production-Grade Workflow'
date: 2022-01-18 22:07:58
tags: [Tech,Study]
---
# Section 6: Creating a Production-Grade Workflow

## Workflow

Our workflow is like this:

![](https://kunren1010.github.io/post-images/docker-images/section-6/%E6%88%AA%E5%B1%8F2022-01-10%2020.56.45.png)

So we are goning to go through this whole workflow.

### Create a React demo Application

First, We need to create a react project, and we need install  *NodeJS* and *npm* to create a react app.

After NodeJS and npm installed, we can run:

```Bash
➜  Project npx create-react-app front

Creating a new React app in /Users/kun/Project/front.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts with cra-template...


added 1358 packages in 28s

166 packages are looking for funding
  run `npm fund` for details

Initialized a git repository.

Installing template dependencies using npm...

added 33 packages in 3s

166 packages are looking for funding
  run `npm fund` for details
Removing template package using npm...


removed 1 package, and audited 1391 packages in 2s

166 packages are looking for funding
  run `npm fund` for details

8 moderate severity vulnerabilities

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.

Created git commit.

Success! Created front at /Users/kun/Project/front
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd front
  npm start

Happy hacking!

```


As we can see: 

1. *npm start* is used for development to run this app
2. *npm test* is used for testing 
3. *npm run build* is used for deployment. If we run this command it will generate the native .js and .html files which used to deployment.

![](https://kunren1010.github.io/post-images/docker-images/section-6/%E6%88%AA%E5%B1%8F2022-01-11%2010.08.52.png)

### Run the Workflow on local machine

#### Development

So what we need to do fristly is creating a Dockerfile to provide a container that can be used in *Travis CI*.

> Dockerfile


```YAML
FROM node:16-alpine

WORKDIR '/app'

COPY package.json .
RUN npm install

COPY . .

CMD ["npm", "run", "start"]
```


If we run this container locally and expose our port, we can get a demo page of react.

Next, Let's create docker-compose.yml to use and manage this container:

So first we want to create a container to run this react app:

> docker-compose.yml


```YAML
version: '3'
services:
  development-container:
    build: .
    ports:
     - '3000:3000'

```


Use *docker-compose up* command, we successfully run this container. And if we want to bind our local files with the container directory, we can do this:

> docker-compose.yml


```YAML
version: '3'
services:
  development-container:
    build: .
    ports:
     - '3000:3000'
    volumes:
     - /app/node_modules
     - .:/app
```


Yes, that is for development. But how to test our application?

#### Test

As I mentioned, use *npm run test* in the container, so we need another container to run the tests.

> docker-compose.yml


```YAML
version: '3'
services:
  development-container:
    build: .
    ports:
     - '3000:3000'
    volumes:
     - /app/node_modules
     - .:/app
  test-container:
    build: .
    volumes:
     - /app/node_modules
     - .:/app
    command: ["npm", "run", "test"]
```


Yeah, basically, we just create another container and override the default command in Dockerfile. So what's next?

#### Deployment

So how to deploy our application? First, we run *npm run build*, and we will get a build directory where there are some .js and .html files. So what we need is a HTTP server that can proxy and expose those files. So we use *Nginx* which is the most popular proxy over the world.

So how to add *Nginx* into our project? Here is our solution:

![](https://kunren1010.github.io/post-images/docker-images/section-6/%E6%88%AA%E5%B1%8F2022-01-11%2020.17.12.png)

![](https://kunren1010.github.io/post-images/docker-images/section-6/image.png)

For this purpose, we need motify our D*ockerfile*. 

> Dockerfile


```YAML
FROM node:16-alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx
COPY --from=builder /app/build usr/share/nginx/html
```


Yes, we need run the *npm run build* command in the first stage, and then copy the *build *directory into *nginx* image.

When we run *docker build .* and *docker run -p 3000:80 b83586647e24*. 80 port is the default port of nginx.

