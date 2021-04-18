# DevOps

Diagram : [link](https://raw.githubusercontent.com/gregwell/university-notes/main/english/devops/images/diagram.png)

## Git

### 1. Git operations

**git squash** - to "squash" in Git means to combine multiple commits into one. You can do this at any point in time, though it is most often done when merging branches.

**git rebase** - :

![images/git-rebase.png](images/git-rebase.png)

### 2. Git flows

- Git flow

<img height="250" src="images/git-flow.png">

- Gitlab flow

<img height="250" src="images/git-lab-flow.png">

- Github flow

<img height="250" src="images/github-flow.png">

- One flow

<img height="250" src="images/one-flow.png">

### 3. Git hooks

- created git hook *commit-msg* which does not allow a commit message that does not contain a keyword at the beginning

```bash
word="GS306504"
isPresent=$(grep ^"$word" $1)

if [[ -z $isPresent ]]
  then echo "Commit message WRONG, $word should be at the beginning"; exit 1;
  else echo "Commit message OK"; exit 0;
fi
```

# Docker

### Dockerfile

**Dockerfile:**

```docker
FROM ubuntu:latest
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y git
```

**In terminal:**

```bash
docker build -t first-docker .
docker run -it first-docker
git clone https://github.com/InzynieriaOprogramowaniaAGH/MIFT2021.git
```

### Docker-compose

`git checkout -b Grupa05-GS306504_Lab04` - created a new branch 

`docker login -u gregwell` - signed in to *dockerhub.com*

`docker build -t gregwell/grupa05gs306504lab03:latest .` - built a docker image

`docker push gregwell/grupa05gs306504lab03` -pushed the image to a registry

**Dockerfile-build**

- creates a new docker container image and configures it to be able to perform the operation of building the chat app from sources (compilation, linking, dependencies, etc.) - **builds with npm**

```docker
FROM ubuntu:latest
ENV TZ=Europe/Warsaw
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo &TZ > /etc/timezone
RUN apt-get -y update
RUN apt-get install -y npm
RUN git clone https://github.com/binhxn/node-chat-app.git
WORKDIR node-chat-app
RUN npm install
```

**Dockerfile-test**

- creates a new docker container image and configures it to be able to perform the chat-app testing operation using the previously built program in *Docker-build*.

```docker
FROM nodechat-build:latest
WORKDIR node-chat
RUN npm test
```

**docker-compose.yml**

- Creates an environment that downloads the latest version of [node-chat-app](https://github.com/binhxn/node-chat-app.git) from the repo, builds it and tests if it works.

```docker
version: '3.3'
services: 
    build:
        container_name: nodechat-build
        image: nodechat-build:latest
        build: 
            context: .
            dockerfile: Dockerfile-build
    test: 
        container_name: nodechat-test
        image: nodechat-test:latest
        build: 
            context: .
            dockerfile: Dockerfile-test
        depends_on:
            - build
```