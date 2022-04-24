---
title: 'Section 7: CI/CD'
date: 2022-01-18 16:10:18
tags: [Tech,Study]
---
# Section 7: CI/CD

![](https://kunren1010.github.io/post-images/docker-images/section-7/%E6%88%AA%E5%B1%8F2022-01-11%2009.07.48.png)

In reality, The whole workflow is more like this. First push our code to *Github* and merge code into *master branch*. Then *Travis CI* fetch all files from *Github*. After running , testing and building (run *npm start*, *npm test *and *npm run build*), Travis CI will deploy the built code into *AWS Hosting*.

So the first step is to create a github repo. And config our Travis CI.

Next, we need to tell Travis to build our image, run our test and delopy our build files to AWS.

![](https://kunren1010.github.io/post-images/docker-images/section-7/%E6%88%AA%E5%B1%8F2022-01-12%2019.58.18.png)

Here is a config that can make Travis know what it should do. that is .travis.yml

> .travis.yml


```YAML
sudo: required
services:
  - docker

before_install:
  - docker build -t docker-react -f Dockerfile.dev .

script:
  - docker run docker-react npm run test -- --coverage

```


Yes, it works fine! and we can see some logs in Travis CI website.

![](https://kunren1010.github.io/post-images/docker-images/section-7/%E6%88%AA%E5%B1%8F2022-01-12%2020.14.53.png)

But for AWS delopment, unfornately I have no free account to do this. So I can just put the .travis.yml file here. It just uses the API of AWS. The whole process is just the same as run test.





