---
title: Docker and Containers - Docker Intro Lab
date: "2018-10-28T22:12:03.284Z"
---

> This lab demonstrates 3 ways of running docker containers,
how to package a Docker image using Dockerfile,
and how to use "bind mount" to map a host directory inside container.

## Learning Outcomes

- How to use container to run a single task
- How to run container interactively
- How to run container in background
- How to package a Docker image and run a container upon it
- How to bind mount a host directory to container


## Prerequisites

- Docker ID, register one [here](https://cloud.docker.com/) if you don't have it

## Operations

### 0: Preparations

- Clone the demo git repository

```bash
git clone --depth 1 https://github.com/dockersamples/linux_tweet_app
```

- Export an environment variable containing your DockerID

```bash
export DOCKERID=<your docker id>
```

### 1: Run some simple Docker containers

#### 1.0: To run a single task

This could be a shell script or a custom app.

```bash
docker container run alpine hostname
```

This creates a container from image _alpine:latest_ and run _hostname_ process inside,
when the process exits, the container stops, but the resources are not deleted, to look up,

```bash
docker container ls --all
``` 

#### 1.1: Interactively 

This connects you to the container similar to the way you SSH into a remote server.

```bash
docker container run --interactive --tty --rm ubuntu bash
```

- _--interactive_ says you want an interactive session.
- _--tty_ allocates a pseudo-tty.
- _--rm_ tells Docker to go ahead and remove the container when it’s done executing.
- run _bash_ as main process in Ubuntu container

Docker has attached to the shell in the container, 
relaying input and output between your local session and the shell session in the container.

#### 1.2: In the background 

For long-running services like websites and databases.

```bash
 docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-pw \
 mysql:latest
```

- _--detach_ will run the container in the background.
- _--name_ will name it _mydb_.
- _-e_ will use an environment variable to specify the root password

This runs a MySQL container with the specifications above.

To run a command inside the container,

```bash
docker exec -it mydb \
 mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
```

To connect to a new shell process inside an already-running container,

```bash
docker exec -it mydb sh
```

### 2: Package and run a custom app using Docker

In this task, we’re going to create a simple NGINX website from a Dockerfile.

- To see the Dockerfile

```bash
cd ~/linux_tweet_app
cat Dockerfile
```

- To create a new Docker image using the instructions in the Dockerfile
    - _--tag_ allows us to give the image a custom name. 
    In this case it’s comprised of our DockerID, the application name, and a version. 
    Having the Docker ID attached to the name will allow us to store it on Docker Hub in a later step
    - _._ tells Docker to use the current directory as the build context

```bash
docker image build --tag $DOCKERID/linux_tweet_app:1.0 .
```

- To start a new container from the image you created

```bash
 docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
```

Any external traffic coming into the server on port 80 will now be directed into the container on port 80.
The format of the _--publish_ flag is *host\_port:container\_port*.

- To shut it down and remove it

```bash
docker container rm --force linux_tweet_app
```

### 3: Modify a running website

When you use a bind mount, a file or directory on the host machine is mounted into a container running on the same host.
This will allow any changes made to the files on the host to be immediately reflected in the container, however,
this will __not__ change the underlying image.

```bash
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \
 $DOCKERID/linux_tweet_app:1.0
```

Use the _--mount_ flag to mount the current directory on the host into _/usr/share/nginx/html_ inside the container

Then make a change to the index page, the change will take effect immediately,

```bash
cp index-new.html index.html
```

## Official Links

[Lab: Docker Intro](https://training.play-with-docker.com/beginner-linux/)