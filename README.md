# Distributed Systems Practice
Notes from learning about distributed systems in [GW CS 6421](https://gwdistsys18.github.io/) with [Prof. Wood](https://faculty.cs.gwu.edu/timwood/)

## Docker Container
### Beginner Level
#### [Video: Why Docker?](https://www.youtube.com/watch?v=RYDHUTHLf8U&t=0s&list=PLBmVKD7o3L8tQzt8QPCINK9wXmKecTHlM&index=23)  
1. Docker is all about speed.
2. Docker is freeing up a lot of tasks, such as keeping existing software updated, keeping it running, fixing its problems, backing it up and so on, which leave us less time to deploy new software.  

#### [Lab: DevOps Docker Beginners Guide](https://training.play-with-docker.com/ops-s1-hello/)  
Steps:  
  
1. Installing docker  
	I'm using mac, so I choose to use brew to install docker. After installing docker by `brew install docker`, I met this problem `docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.`. Then, I found the solution from [friederbluemle](https://stackoverflow.com/questions/44084846/cannot-connect-to-the-docker-daemon-on-macos) on stack overflow. If you don't wanna jump into that link, here is the solution.   
	
	> `brew cask install docker` Then launch the Docker app. Click next. It will ask for privileged access. Confirm. A whale icon should appear in the top bar. Click it and wait for "Docker is running" to appear. You should be able to run docker commands now.  
  
2. Running my first container  
	Command: `docker conntainer run hello-world`  
	![Imgur](https://i.imgur.com/NiR3Wan.png)  
  
  
Notes:  

1. Containers and images are different concept.  
	__Images__: The file system and configuration(read-only) of application which are used to create containers.   
	__Containers__: Containers are running instances of Docker images. Containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers and runs as an isolated process in user space on the host OS.  
  
2. Container Isolation  
	Even though two containers share the same image, these two instances are separated from each other. No matter what happens in an instance, it won't affect other instances. 

### Intermediate Level
#### Containers:
##### [Video: What are Containers?](https://www.youtube.com/watch?v=EnJ7qX9fkcU)
1. Image Layering  
	The images are arranged in a hierarchy structure. Let's say, you have an operating system busybox, then sshd and perl running on busybox, and your app at the top layer of the hierarchey structure.
2. Docker File  
	A docker file is an environment in a text file. The start of a docker file is `From:`. `From` is the parent image where this docker file is inheriting from. `From` is followed by any number of things that we want to configure the image that this docker file is going to create. So, a docker file ultimately ends up creating an image.
3. Transformation  
	We can build an image by a docker file. We can use images to run containers. We can do modification in containers then commit the containers into new images. So, we can move between these stages.
4. Contents in a container  
	All of the dependencies above the kernel are packaged inside of the container. So, when you run the container in an operating system, you don't acctually install anything. Everything is already inside the container, and the application just sits on its own stack. So, when you delete these images, it's all gone. Then you can run stuff on your environments without polluting them.  
5. Docker Daemon  
	The docker daemon is the engine of the docker container.
6. Docker Client  
	The CLI tool to manipulate docker daemon.
7. Registry  
	A registry is a thing that contains images. You can pull and push from the registry at will.
  
##### [Video: VMs Versus Containers](https://www.youtube.com/watch?v=L1ie8negCjc)  
1. Where VM lives  
	VM lives between the phyiscal infrastructure and OS layer. It masks all the details of delegation of the hardware.
2. Where Docker Container lives  
	Docker container lives between the OS and your app. The OS can only have the minimum stuffs, but the container has all the OS dependencies for the app running in this container.  
	
##### [Lab: Docker Intro](https://training.play-with-docker.com/beginner-linux/)  
1. When you already start a container, and you want to execute command, use `docker container exec -it <Command>`
2. When you want to connect to a new shell process inside an already-running container, you can use `docker container exec -it <Shell>`
3. Structure of Docker File  
	Docker file starts with `FROM`, which shows what image does this image lie on. `COPY` copies files from the Docker host into the image, at a known location. `EXPOSE` documents which ports the application uses. `CMD` specifies what command to run when a container is started from the image. Notice that we can specify the command, as well as run-time arguments.  
4. Build image from docker file  
	e.g. `docker image build --tag jzhzj/linux_tweet_app:1.0 .`  
	Use the `docker image build` command to create a new Docker image using the instructions in the Dockerfile.  
	- `--tag` allows us to give the image a custom name. In this case it’s comprised of our DockerID, the application name, and a version. Having the Docker ID attached to the name will allow us to store it on Docker Hub in a later step  
	- `.` tells Docker to use the current directory as the build context
Be sure to include period (.) at the end of the command.  
5. Modify the running website  
	If you wanna modify a running container, you can mount a host directory into a directory inside the container. When you modify files in the host directory, it reflects at once in the container. However the image from which the container starts from does not change.  
6. Publish your images   
	First: `docker login`  
	Next: `docker image push <Image name:Version>`  
	Finally: You can check your newly-pushed Docker images at `https://hb.docker.com/r/<your docker id>/`
  
##### [Lab: Doing more whith Docker Images](https://training.play-with-docker.com/ops-s1-images/)
1. To restart an stopped container  
	`docker container start CONTAINER_ID` This command just start the container but doesn't show the terminal. Use `docker exec -it CONTANINER_ID SHELL` to show a terminal.  
2. Commit a modified container  
	`docker container commit CONTAINER_ID`  
	This will create a new image, except it has no information in the REPOSITORY or TAG columns.  
	`docker image tag <IMAGE_ID> ourfiglet`  
	Adding this information to an image is known as tagging an image. From the previous command, get the ID of the newly created image and tag it.  
3. Build an image with a docker file  
	Prepare a Node.js file in your work dir.
	e.g.  
	>
	var os = require("os");  
	var hostname = os.hostname();  
	console.log("hello from " + hostname);  
	
	Prepare a Dockerfile in your work dir.  
	e.g.  
	>
	FROM alpine  
	RUN apk update && apk add nodejs  
	COPY . /app  
	WORKDIR /app  
	CMD ["node","index.js"]  
	
	Use `docker image build -t hello:v0.1 .` to build the image.  
4. Terminology  
	- Layers - A Docker image is built up from a series of layers. Each layer represents an instruction in the image’s Dockerfile. Each layer except the last one is read-only.
	- Dockerfile - A text file that contains all the commands, in order, needed to build a given image. The [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) page lists the various commands and format details for Dockerfiles.
	- Volumes - A special Docker container layer that allows data to persist and be shared separately from the container itself. Think of volumes as a way to abstract and manage your persistent data separately from the application itself.

##### [Video: VMs Versus Containers Deep Dive](https://www.youtube.com/watch?v=PoiXuVnSxfE)
1. Size  
	The size of an image of a VM containing user application and OS kernel can range from hundreds of megabytes to tens of gigabytes.  
	The size of an image of a container containing the application and all the dependencies that the application requires to run can range from tens of megabytes up to gigtabytes.
2. Security concern
	There is almost no way for attackers to hack the host running a VM from the processes running inside the VM. However, since the docker containers using the same kernel, it's much easier to attack the host from the process inside a container if there are bugs inside of the kernel.
3. Boot time  
	The boot time for processes in both techniques are almost the same, let's say 500 ms. The time for start up the kenel of VM can be up to 3 or 4 seconds, while there is almost no time taken to boot a container. There is only two steps to start up a container: one is a kernel operation which is setting up the process sandbox and the other one is starting up the application itself.

#### Networking and Orchestration:

##### [Lab: Docker Networking]()

##### [Lab: Swarm Mode Introduction]()

##### [Video: Kubernetes vs Swarm]()

##### [Video: Kubernetes in 5 Minutes]()

##### [Kubernetes]()

##### Install Docker on a cluster of EC2 VMs and use Kubernetes to orchestrate them

#### Bring it all together:

##### [AWS Tutorial: Break a Monolith Application into Microservices]()

## Area 2
> Include notes here about each of the links
