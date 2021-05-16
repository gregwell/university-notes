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

## Lab 5: Jenkins as CI/CD driver

### **Task:**

1. *familiarize yourself with the diagram discussed in class.*
2. *familiarize yourself with the Jenkins software: [https://www.jenkins.io/](https://www.jenkins.io/).*
3. *create a Jenkins Docker container using the instructions [https://www.jenkins.io/doc/book/installing/docker/](https://www.jenkins.io/doc/book/installing/docker/)*
4. *Upload your Jenkins image to DockerHub just like the previous images.*
5. *from within Jenkins running in a container (point 2) run docker-compose created within Lab04.*
- *we assume Jenkins is running in a container*
- *we assume that after Lab04 docker-compose.yaml file is in our repository*
- *we assume that from Jenkins we download the repo where our docker-compose.yaml file is*
- *assume Jenkins runs the docker-compose.yaml file and creates the environment with Lab04*
- *note that running docker-compose requires.yaml of the Docker software*

### **Notes:**

- Created a bridge network in Docker using the following docker network create command

    `docker network create jenkins`

- In order to execute Docker commands inside Jenkins nodes, downloaded and run the *docker:dind* Docker image

```docker
docker run --name jenkins-docker --rm --detach \
  --privileged --network jenkins --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 docker:dind --storage-driver overlay2
```

- To customise official Jenkins Docker image created a Dockerfile:

```docker
FROM jenkins/jenkins:2.277.2-lts-jdk11
USER root
RUN apt-get update && apt-get install -y apt-transport-https \
       ca-certificates curl gnupg2 \
       software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/debian \
       $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean:1.24.6 docker-workflow:1.26"
```

- Built a new docker image from this Dockerfile

```docker
docker build -t myjenkins-blueocean:1.1 .
```

- Run new docker image as docker container

```docker
docker run --name jenkins-blueocean --rm --detach `
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 `
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
  --publish 8080:8080 --publish 50000:50000 `
  --volume jenkins-data:/var/jenkins_home `
  --volume jenkins-docker-certs:/certs/client:ro `
  myjenkins-blueocean:1.1
```

- went to [localhost:8080](http://localhost:8080) to unlock jenkins
- created new jenkins job *freestyle* of type
- configured to execute the following shell scirpt as a build step:
```
cd Grupy/Grupa05/GS306504/Lab04
pwd
ls
docker --version
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
docker-compose up
```

### **Issues:**

1. **Permissions denied when downloading docker-compose from the url with curl command**

**Cause**:

- the user that is executing the script in jenkins is 'jenkins', such operations(saving to system folders) can be done only by root

**Solution**:

- addded 'jenkins ALL (ALL) NOPASSWD: ALL' to /etc/sudoers file
- *(temporary solution! need to be solved permamently in the future)**

**2. Never-ending loading after running docker-compose in jenkins.**

**Cause**:

- Dockerfile-build do not finish its work
- Problem with understanding how docker works.
- If I start an application via 'npm start' and don't terminate it then jenkins will wait until it terminates. In this case, forever.
- Dockerfile-build should just clone the repo, install dependencies with 'npm install'.
- The Dockerfile-test file tests if the program works correctly, no need to do it in Dockerfile-build.

**Solution**:

- Delete 'npm start' from Dockerfile-build

## Lab 6: Release pipeline

### **Task:**

*In today's class you will play the role of a DevOps developer whose task is to prepare a release pipeline plan (sequence of implementation steps) and to prepare a document (in pdf format) presenting the planned process. You do not have to implement the process at this point! Before you start implementing them, read the Jenkinsfile concept very carefully ([https://www.jenkins.io/doc/book/pipeline/](https://www.jenkins.io/doc/book/pipeline/) with subsections as needed). Eventually in the future a large part (or even the whole) of the plan you create will have to be included in a Jenkinsfile.*

*The program that is the subject of the pipeline is the one you chose in Lab 4. Remember that in order to have your own copy of the open source program repository (e.g. to modify it, place additional files, git hooks, etc.) you can use the "fork" option, as described here: [https://guides.github.com/activities/forking/](https://guides.github.com/activities/forking/).*

1. *the plan file is to contain or meet the following requirements:*
- *title page with information who, for what application, when, with planned use of what technologies prepared this plan*
- *activity diagram (UML Activity diagram) showing in a schematic way all activities and main decisions. You can (and even should) refer to the "CI/CD Pipeline" diagram discussed in the lecture, but each of the general steps should be 1) detailed - broken down into specific actions*
1. *indicate the specific technology planned to be used in a given step.*
- *an implementation diagram (UML Deployment Diagram) analogous to the one discussed in Lab 5. It should be adjusted to reality, missing elements should be added, those not present or not relevant should be removed.*
- *verbal discussion of all steps planned to be taken within the CI/CD Pipeline - concretely, not theoretically*
- *is to use the following technologies (maybe others)*
1. *git (including git hooks, GitHub)*
2. *docker (including docker compose)
(3) docker registry (including DockerHub)*
3. *Jenkins (including Jenkinsfile)*

### Release pipeline

**App**: Node chat app

**Date**: 16.05.2021

**Tech stack:** Github, Docker, Dockerhub, Jenkins

**What is what;**

- A **release** is a collection of artifacts in your DevOps CI/CD processes.
- An **artifact** is a deployable component of your application

**Activity diagram:**

![acivity diagram](images/activity-diagram.png)

**Deployment diagram:**

![deployment diagram](images/deployment-diagram.png)

**Jenkins pipeline description:**

1. Detection of changes in repository - a new commit
2. Jenkins fetches data and starts deployment process based on Jenkinsfile.
3. Docker-compose.yml consists of two images
    - build: simply clones the repository and configures it, install dependencies etc.
    - test: runs tests
4. If build container fails to build itself the notification is sent to Jenkins Dashboard and the process stops.
5. If build container successfully builds itself, then the test container is to be run. If the tests fail the process stops and notification to Jenkins Dashboard is sent.
6. Then, a new image is published in dockerhub (new version) and the code is deployed to production.