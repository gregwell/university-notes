# DevOps

Diagram : [link](https://raw.githubusercontent.com/gregwell/university-notes/main/english/devops/images/diagram.png)

# Git

## 1. Git operations

**git squash** - to "squash" in Git means to combine multiple commits into one. You can do this at any point in time, though it is most often done when merging branches.

**git rebase** - :

![images/git-rebase.png](images/git-rebase.png)

## 2. Git flows

- Git flow

<img height="250" src="images/git-flow.png">

- Gitlab flow

<img height="250" src="images/git-lab-flow.png">

- Github flow

<img height="250" src="images/github-flow.png">

- One flow

<img height="250" src="images/one-flow.png">

## Git hooks

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

## Lab 3. Simple Dockerfile

**Task:** 

1. *create a Dockerfile that is based on the latest ubuntu (ubuntu:latest).*
2. *add it to your branch.*
3. *make sure it has git installed if it doesn't already have it (in the Dockerfile).*
4. *run the container, download our repository to it. Make sure your branch is there.*

**Files:**

Dockerfile:

```docker
FROM ubuntu:latest
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y git
```

In terminal:

```bash
docker build -t first-docker .
docker run -it first-docker
git clone https://github.com/InzynieriaOprogramowaniaAGH/MIFT2021.git
```

## Lab 4. Running node-chat-app in a container

**Task:**

1. *create a personal branch for this class according to the scheme from the previous week (e.g.: Group01-KM123456_Lab03). Remember to create it from your group branch!
1.1 Publish an image from the previous class in the Docker HUB ([https://hub.docker.com/](https://hub.docker.com/))*
2. *Write yourself or find any messeging app (opensource messeging app) on the internet.*
3. *Build agent: Create a new Docker container image (new Dockerfile) and configure it to be able to perform the operation of building a "messenger" from source (compilation, linking, dependencies, etc.)*
4. *test agent: Create a new docker container image (new Dokerfile) and configure it to be able to perform the operation of testing the "communicator" using the previously built program in point 3.*
5. *create docker-compose.yml file ([https://docs.docker.com/compose/](https://docs.docker.com/compose/)). Add it to your branch. Using images 3) and 4) create an environment that:*
- *clones the latest version of "messenger" from the repo*
- *builds it*
- *test if it works (you can use more than 1 instance of image 4) if necessary.*

**Ex. 1**

`git checkout -b Grupa05-GS306504_Lab04` - created a new branch 

`docker login -u gregwell` - signed in to *dockerhub.com*

`docker build -t gregwell/grupa05gs306504lab03:latest .` - built a docker image

`docker push gregwell/grupa05gs306504lab03` -pushed the image to a registry

**Ex. 2-5**

**Dockerfile-build**

```docker
FROM node:latest
WORKDIR /data
RUN git clone [https://github.com/binhxn/node-chat-app.git](https://github.com/binhxn/node-chat-app.git) /data/app
WORKDIR /data/app
RUN npm install
EXPOSE 3000
CMD ["npm", "start"]
```

**Dockerfile-test**

```docker
FROM nodechat-build:latest
WORKDIR .
CMD [ "npm", "test" ]
```

**docker-compose.yml**

```docker
version: '3.3'
services: 
    build:
        container_name: nodechat-build
        image: nodechat-build:latest
        ports:
            - "3000:3000"
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

**Notes:**

- `git clone` command is usually run outside of the Docker. There are a couple of reasons for this: ([stackoverflow answer](https://stackoverflow.com/questions/63783323/getting-copy-failed-stat-when-trying-to-copy-package-json-file-in-docker-on-dig))
    - it lets you check the Dockerfile into the repository
    - it lets you build an image out of something you haven't committed yet
    - Docker layer caching doesn't prevent you from seeing changes in the source repository
    - you don't want your GitHub credentials from being visible in plain text in docker history.
- Exposing vs publishing ports:
    - `EXPOSE` only opens the port in the container, making it accessible by other containers.
        - The EXPOSE instruction does not actually publish the port. It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published. To actually publish the port when running the container, use the -p flag on docker run.. Like VonC mentioned, the run -p part can be done with docker-compose.yml (the ports field). [from [docker documentation](https://docs.docker.com/engine/reference/builder/#expose)]
    - `"3306:3306"` will publish the port on the host, making the same port accessible from the host. It maps container port to the host.

    ```
    ports:
     - "3306:3306"
    ```