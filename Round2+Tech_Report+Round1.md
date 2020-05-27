* Author: [@unlimitediw](https://github.com/unlimitediw)
* Resources: 
	- [CS 6421 Distributed Systems - Fall 2018, provided by @Prof. Tim Wood](https://gwdistsys18.github.io/)
	- [AWS Tutorail](https://aws.amazon.com/getting-started/tutorials/)
	- [Docker Documentation](https://docs.docker.com/)

<a name="menu"></a>
## Docker and Containers (Intermediate Round2)
1. [Containers](#containers)  
    * [Containers Introduction](#whatcontainer)
    * [VMs vs Containers](#containervsvm)
    * [Docker Introduction](#dockerintro)
    * [Docker Image](#dockerimage)
2. [Networking and Orchestration](#networkOrch)
    * [Introduction](#netorcintro)
    * [Docker Networking Hands-on Lab](#dockernetlab)
    * [Swarm Mode Introduction Lab](#swarm)
    * [Docker Swarm vs Kubernetes](#swarmkuber)
    * [Break a Monolith Application into Microservices](#breakservice)

## Cloud Web Application (Beginner Round2)
1. [Launch a VM](#launchvm)
2. [Intro to S3](#wps3)

## Technical Report
[My SVM and Sagemaker](#mysagemaker)

## BIG DATA AND MACHINE LEARNING (Intermediate Round1)
1. [Hadoop Introduction](#introduction)
2. [Analyze Big Data with Hadoop](#bigdata)
3. [Introduction to Amazon Simple Storage Service](#s3)
4. [Introduction to Amazon Redshift](#redshift)
5. [Intro to Amazon Machine Learning](#introml)
6. [Build a Machine Learning Model](#buildml)
7. [AWS SageMaker Overview](#viewsegmaker)
8. [AWS SageMaker](#segmaker)
9. [Build a Serverless Real-Time Data Processing APP](#app)
10. [Conclusion for the Unicorn project](#unicorn)


*********************

# Docker and Containers 

<a name ="containers"></a>
## Containers
<a name ="whatcontainer"></a>
### Containers Introduction
[Back to Menu](#menu)
> Definition: A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another ([www.docker.com](https://www.docker.com/resources/what-container)).  

> Not just a Container  
* Runtime OS (e.g linux): inside are processes. The container is used to isolate one process to other processes (sandbox)
  * The container process is tied in with the lifecycle of container.
* Container Image
  * Container is the instance of image.
  * Contains *binary* state (representations): such as VMDK(Virtual Machine Disk) is a disk image OVA(Open Virtual Appliance) is an image for VM.
  * Tree effectively concentrate specific things in specific places.
* Dockerfile
  * Dockerfile is an environment in a text file.
  * Dockerfile configures and ends up with creating an Image.
  * Syntax(e.g)
    * From: busybox (within here we can run any number of things that we want to configure the image that the docker file is going to create.
* Runtime OS and Dockerfile can both communicate with Image.
* Docker Host(Tie OS, Image and Dockfile together)
  * Registry outside(pull and push the image you need)
  * Image cache inside (satisfy pulling and pushing of registry)
  * Client outside (Control pull, push, create, run and commit image) 
    * Client can also do network and storage configuration
    * Client manage container lifecycle
  * Deamon inside (Client talk to deamon with API) 
  * Storage inside(but beyond lifecycle) (Volume, a persistent area of storage) or network (push it out on network)
    * Use it if it wants to persist any data beyond the lifecycle of the container
    * Network will also allow persistence after container end
  * Container Life Cycle
> Summary: Container is a sandbox that allows user to isolate their processes while Container Image (created by dockerfile) likes a manager allows sharing and concentrates specific things in specific area. The Docker Host links all these things together and provides a standard, lightweight and secure Container.
<a name ="containervsvm"></a>
### VMs vs Containers
[Back to Menu](#menu)
> VM  
  * APP -> OS -> VM -> Hypervisor -> Physical (notice that Application virtualization and Hosted virtualization are onside of OS)
  * VM: NIC(Network interface), Storage and Size.  
  * Hypervisor: NIC, Storage, Agents and Kernel
  
> Container  
  * App + OS Dependences = Container -> OS -> VM -> Hypervisor -> Physical (VM may be combined using with Container  

> Performance:
* Size:
  * VMs: range from 100MB to 100GB, everything but hardware is comprised inside the disk image (kernel, init system, user space programs and applications).
  * Containers: range from 10MB to 1GB. The things inside the container is exclusively the process that is being run. (may create a container based on a base container that contains all the user space from a specific Linux distribution, such as Ubuntu base container which only contains the package manager and a few other user space tools)
* Isolation:
  * VMs: extremely difficult to escape the boundary of VM.
  * Containers: insecure, as secure as the kernel they are running on. Escape the sandbox an reach the kernel.
* Boot Time:
  * VMs: several startup times which can be divided in two sections. One is the system check section that includes the x86 post, the EFI (Extensible Firmware Interface) or boot check, the kernel boot and the init startup (3~4 seconds in total). The other one is startup of the process itself takes 0.5 second.
  * Containers: run the process and setup the sandbox. Kernel operation takes no time while the startup of process just takes about 0.5 second.
> Summary: VMs is more secure, strong in isolation and can be combined with Container (e.g Docker: VM + Container) while Container is more lightweight (less resource consumption, startup faster, easy to deploy and specify resource just for application).
<a name ="dockerintro"></a>
### Docker Introduction
[Back to Menu](#menu)
> Foreword:
  * Docker unlocks the potential of your organization by giving developers and IT the freedom to build, manage and secure business-critical applications without the fear of technology or infrastructure lock-in ([www.docker.com](https://www.docker.com/why-docker)).
  * I will go through the beginner tutorial with my explanations for some Linux commands such as 'cat' and 'echo' and interpret the meaning of each command. There are three tasks: run and interact a Ubuntu container, a MySQL container and a custom app using docker and it will give us a view of the processes inside the container and the way to build and modify a container with Image.  

> Task 0: Prerequisites
  * It need a linux environment and a DockerID
> Task 1: Run some simple Docker containers
  * Three different ways to use containers:
    * To run a single task: This could be a shell script or a custom app.
    * Interactively: This connects you to the container similar to the way 
    * In the background: For long-running services like websites and databases
  * Run a single task in an Alpine Linux container
    * Run ```docker container run alpine hostname```. The container will start, execute hostname command then exit
        * Pull ```alpine:latest``` image from Docker Hub
        * The container's hostname will be displayed  
        ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/alpineHostname.PNG)
    * Check container states after hostname processing exits(container stop) by running ```docker container ls --all```
        * Docker doesn't delete resources by default and the container still exist in the "Exited" state  
        ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/lsall.PNG)
  * Run an interactive Ubuntu container
    1. Run a Docker container and access its shell by "docker container run --interactive --tty --rm ubuntu bash"
      * ```--interactive``` says you want an interactive session.
      * ```--tty``` allocates a pseudo-tty.
      * ```--rm``` tells Docker to go ahead and remove the container when it's done executing.
      * bash(which is a shell, a user interface for access to an operating system) is used as main process.
      * Now you are in the bash shell.
      * When the container start you will drop into the bash shell with the default prompt root@<container id>:/#. Docker has attached to the shell in the container, relying input and output between your local session in the container.
    2. Run ```ls /```, ```ps aux``` and ```cat/etc/issue```
       * ```ls /``` list the contents of the root director in the container.
       * ```ps aux``` show running processes in the container.
          * USER PID ... STAT START TIME COMMAND
          * root 1 ... SS 00:12 0:00 bash
          * root 14 ...R+ 00:22 0:00 ps aux  
   ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/PreviewUbuntu.PNG)
    3. Type ```exit``` to leave the shell session. This will terminate the bash process, causing the container to exit.
  * Run a background MySQL container
    1. Run a new MySQL container with
       ####
           docker container run \
           --detach \
           --name mydb \
           -e MYSQL_ROOT_PASSWORD=my-secret-pw \
           mysql:latest
        * ```--detach``` will run the container in the background.
        * ```--name``` will name it mydb.
        * ```-e``` will use an environment variable to specify the root password.

    2. List the running containers ```docker container ls```.  
    ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/lsall.PNG)
    3. Check what's happening in your containers by ```docker container logs``` and ```docker container top```.  
    ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/dblogs.PNG)
    ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/deamon.PNG)
    4. List the MySQL version using ```docker exec```(which allow you command inside a container) and run ```mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version```.  
    ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/checkVersion.PNG)
    5. You can also use ```docker exec -it mydb sh``` to connect to a new shell process inside an already running container
> Task 2: Package and run a custom app using Docker  
  
  * Build a simple website image
  1. ```cd ~/linux_tweet_app```
  2. ```cat Dockerfile```
     * cat: concatenate command in linux.
     * From: specifies the base image to use as the starting point for this new image you're creating. For this example we're starting from nginx:latest.
     * COPY: copies files from the Docker host into the image, at a known location. In this example, COPY is used to copy two files into the image: index.html. and a graphic that will be used on our webpage.
     * EXPOSE: documents which ports the application uses.
     * CMD: specifies what command to run when a container is started from the image (both command and run-time arguments).  
     ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/displayContent.PNG)
  3. ```export DOCKERID=unlimitediw```
  4. ```echo $DOCKERID```
  5. Use the ```docker image build``` command to create a new Docker image using the instructions in the Dockerfile
   * ```--tag``` allows us to give the image a custom name.
   * ```.``` tells Docker to use the current directory as the build context.  
   ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/DockerImageBuild.PNG)
  6. Use the "docker container run" command to start a new container from the image you created.
    * use ```--publish``` to publish port 80 inside the container onto port 80 on the host. (80 port to 80 port directly in container).
    * ``--publish``` format flag ```host_port:container_port```.
  7. ```docker container rm --force linux_tweet_app``` shut down and remove.
> Summary: In this docker tutoriall, The image instance of the container is built by pulling from the DockerHub and Dockerfile. Each of the process is running in its own container, and to manage these container you need to go inside of it or exit. The hierarchical structure, isolation of container and convenient commands make the process management becomes much easier, secure and standard.
<a name ="dockerimage"></a>
### Docker images
[Back to Menu](#menu)
> Foreword:
   * Definition: Docker Images are defined by Dockerfile which defines what goes on in the environment inside your container ([www.docker.com](https://docs.docker.com/get-started/part2/#your-new-development-environment)).  
   * This part is mainly considering about how to create my own Image and more deeply how to build DockerFile and use it to create image. At the same time, there are many important concepts such as 'layers' and 'volumes' and 'image inspection'
> Image creation from a container  
* Running the bash shell inside the container and intalling figlet package in the container to customize things.
#
    docker container run -ti ubuntu bash  
    apt-get update
    apt-get install -y figlet
    figlet "hello docker"
* Create an image to share  
To see a list of files that were added or changed when installed figlet. (a little like logs)
#
    docker container ls -a
    docker container diff 63f7e97d2f73  
To commit the container and create an image out of it
#
    docker container commit 63f7e97d2f73  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/ImageCreation.png)  
#
    docker image tag 1380f5719fab ourfiglet
    docker image ls
    docker container run ourfiglet figlet hello  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/DockerOurfiglet.png)  
We can create a container and add all libraries and binaries in it and then commit it to create an image. We can use the image s pulled from the Docker Store and share this image by pushing it to a registry somewhere.  
> Image creation using a Dockerfile  

Instead of creating a static binary image, we can also use Dockerfile to create an image. Dockerfile supplies the instructions for building the image which is uselful to manage changes (how an image is built). Dockerfiles are simply text files and can be managed as source code.  
> Dockerfile - A text file that contains all the commands, in order, needed to build a given image. The Dockerfile reference page lists the various commands and format details for Dockerfiles.  
The following work will start by creating a file which I retrieve the hostname and display it.
#
    var os = require("os");
    var hostname = os.hostname();
    console.log("hello from " + hostname);  
Then create a Dockerfile  
#
    FROM alpine
    RUN apk update && apk add nodejs
    COPY . /app
    WORKDIR /app
    CMD ["node","index.js"]
Build first image out of this Dockerfile and name it "hello:v0.1"
#
    docker image build -t hello:v0.1 .
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/DockerfileBuildImage.png)  
* Start a container to check that the application runs correctly
#
    docker container run hello:v0.1
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/Hellofrom.png)  
* Some Tips:
  * To build index.js in this linux os. First type  
  #
      vi index.js
  And hit the "i" key to edit.
  End it with "esc" and then type :wq which will save the file and take me back to the command prompt. 
  * To build Dockerfile is typically the same.
  #
      vi Dockerfile
  To verify the Dockerfile use the command "cat Dockerfile"  
> Review procedure  
  1. Specifies a base image to pull FROM - the alpine image we used in earlier labs.
  2. Then it RUNs two commands (apk update and apk add) inside that container which installs the Node.js server.
  3. Then we told it to COPY files from our working directory in to the container. The only file we have right now is our index.js.
  4. Next we specify the WORKDIR - the directory the container should use when it starts up.
  5. And finally, we gave our container a command (CMD) to run when the container starts.
> Image Layers
  * The images are built in *layers*, the following are *layer* learning steps
  > Layer - A Docker image is built up from a series of layers. Each layer represents an instruction in the image's Dockerfile. Each layer except the last one is read-only.  
  Check out the image created earlier by 
  #
      docker image history 723423834c7e
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/DockerImageHistory.png)  
  In the history command output, the original Alpine layers are at the bottom of the list and then each customization we added in our Dockerfile is its own step in the output. This is a powerful concept because it means that if we need to make a change to our application, it may only affect a single layer!
  #
      echo "console.log(\"this is v0.2\");" >> index.js
      docker image build -t hello:v0.2 .
  Then it is found that it uses cache in 2/5 steps
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/UsingCache.png)
  * Docker recognized that we had already built some of these layers in our earlier image builds and since nothing had changed in those layers it could simply use a cached version of the layer, rather than pulling down code a second time and running those steps. 
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/Layers%26Cache.png)  
> Image Inspection  
* Docker has an inspect command for images and it returns details on the container image, the commands it runs, the OS and more.
#
    docker image inspect alpine
* list of information (JSON format):  
  * the layers the image is composed of
  * the driver used to store the layers
  * the architecture / OS it has been created for
  * metadata of the image
  * …
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/ListOfInformation.png)  
* List of layers
#
    docker image inspect --format "{{ json .RootFS.Layers }}" alpine
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/ListOfLayers.png)  
Alpine is just a small base OS image so there’s just one layer:
But when looking at custom Hello image, we can see three layers in our application
#
    docker image inspect --format "{{ json .RootFS.Layers }}" <image ID>
* Docker Enterprise Edition includes private Trusted Registries with Security Scanning and Image Signing capabilities so you can further inspect and authenticate your images. In addition, there are policy controls to specify which users have access to various images, who can push and pull images, and much more.
* Another important note about layers: each layer is immutable. As an image is created and successive layers are added, the new layers keep track of the changes from the layer below. (which is important for both security and data management.)
* Applications that create and store data (databases, for example) can store their data in a special kind of Docker object called a volume  
> volume - A special Docker container layer that allows data to persist and be shared separately from the container itself. Think of volumes as a way to abstract and manage your persistent data separately from the application itself.  

> Summary: In this part, I create the dockerfile(text) directly on Linux and take it to build my own image and run the container. The general process of it is FROM, RUN, COPY, WORKDIR and CMD and we also need to consder the version control of image layers and we can do it by image inspection.


<a name ="networkOrch"></a>
## Networking and Orchestration

<a name ="netorcintro"></a>
### Introduction
[Back to Menu](#menu)
> Docker Networking
* Docker Networking connect docker together.

> Docker Orchestration
* Container orchestration is used to manage containers in different ways ([Isaac Eldridge](https://blog.newrelic.com/author/isaaceldridge/)):
  * Provisioning and deployment of containers
  * Redundancy and availability of containers
  * Scaling up or removing containers to spread application load evenly across host infrastructure
  * Movement of containers from one host to another if there is a shortage of resources in a host, or if a host dies
  * Allocation of resources between containers
  * External exposure of services running in a container with the outside world
  * Load balancing of service discovery between containers
  * Health monitoring of containers and hosts
  * Configuration of an application in relation to the containers running it

<a name ="dockernetlab"></a>
### Docker Networking Hands-on Lab
[Back to Menu](#menu)
> Foreword
  * This lab is about Key Docker Networking concepts learning such as 'bridge' and 'overlay networking' and finally will create a service and test it. In this lab, I specifically create a gwuservice and test some different value to check the key point like vip address and ping outside/inside.
  
> Networking Basic:
* The Docker Network Command List:
  * ```docker network```: This is the main command for configuring and managing container networks which allows you to create a network, list existing network, inspect network, remove networks and connect/disconnect networks.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1dockernetwork.PNG)
  * ```docker network ls```: Have a view of the existing container networks on the current Docker host. From the terminal, we can see that there are 'brige', 'host' and 'none; in the container network
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1dockernetworkls.PNG)
  * ```docker network inspect```: This command is used to view network configuration details which includes the details of name, ID, driver, IPAM(Internet Protocol Address Management) driver, subnet info, connected container and some boolean values such as Ingress, Internal and so on. Apart from the tutorial command ```docker network inspect bridge``` I alse try the ```docker network inspect host``` to get the host configuration details which are showed below.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1dockernetworkhost.PNG)
  * As we can see, bridge provides more options such as 'enable_icc' and 'host_binding_ipv4'.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1bridgehostdiff.PNG)
  * ```docker info```: This command can list the information about a Docker installation.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1dockerinfo.PNG)

> Bridge Networking
* Basic steps:
  * We first list the docker network with command ```docker network ls``` and we can find that all the network and driver are connected. However, they just has the same name! Taking the 'bridge' as an example, It has the same network and driver name and is scoped locally which means that the network only exists on the Docker host. All networks using the bridge driver which provides single-host networking and all networks created with the bridge driver are based on a Linux bridge.
  * Use the ```apk update``` and ```apk add bridge``` commands to fetch and add bridge packages.
  * Use ```brctl show``` to list the bridges on the Docker Host and it is showing that there is no interface connected to it currently.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1bridgelist.PNG)
  * We can see the details of the 'bridge0' with command ```ip a```.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1ipa.PNG)
* Connect a container
  * The bridge network is the default network for new container.
  * ```docker run -dt ubuntu sleep infinity```: This command can create a new Ubuntu network.
  * I create two containers here and use ```docker ps``` to verify my example containers.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1dockerps.PNG)
  * And as there is not network is specified on the ```docker run``` command, the containers will be added to bridge network.
  ![](containersnow)
  * Now we use the ```docker network inspect bridge``` again to show the new containers connect to the bridge.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1bridgeconnects.PNG)
* Test network connectivity
  * Get the IP address of my own environment by ```ifconfig``` and ping it by ```ping -c5 172.17.0.1```. The replies show that the Docker host can ping the container over the bridge network. The container can also ping the outside ip such as ```ping -c5 github.com```.
  * After install the ping program by ```apt-get update && apt-get install -y iputils-ping```, we can ping both the Ip address inside of the container.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1pinginside.PNG)
  * Remeber to stop the container by ```docker stop containerid``` after ```exit```.
* Configure NAT for external connectivity
  * In this step, we will create a new NGINX container and map port 8080 on the Docker host to port 80 inside of the container which means the traffic that hits the Docker host on port 8080 will be passed on to port 80 inside of the container.
  * The container image file is pulling from "library/nginx" and we start it by ```docker run --name web1 -d -p 8080:80 nginx```. Run ```docker ps``` to see the container details and find the Port of it is showing as "0.0.0.0:8080->80/tcp". It is show that the web1 container is running NGINX and port maps port 8080 on all host interface to port 80 inside the web1 container which makes the port mapping effectively and accessible from external sources by the Docker hosts IP address on port 8080.  
  * Connect with outside world (web server) with this port now ```curl 192.168.0.32:8080```.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1nginxsuccess.PNG)
> Overlay Networking
* Works: Initialize a new Swarm, join a single worker node and verify the operations worked.
* Initiliaze a Swarm node by ```docker swarm init --advertise-addr $(hostname -i)``` and join it by ```docker swarm join ...``` and check it with ```docker node ls```.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1swarmjoinnode.PNG)
* Create an overlay network
  * Create the overlay network by ```docker network create -d overlay overnet``` and use ```docker network ls``` to verify it
  * As we can see there are to overlay (driver) network named "ingress" and "overnet" in the scoped in the swarm. And be attention the overnet network should only appear when a host runs a task from a service that is created on the network and we can use ```docker network inspect overnet``` to show more details.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1overlaynetwork.PNG)
  * Create a service: create a new service called gwuservice on the overnet network with two tasks/replicas.
  ####
      docker service create --name gwuservice \
      --network overnet \
      --replicas 2 \
      ubuntu sleep infinity
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1gwuservice.PNG)
  * And present it by ```docker service ps gwuservice```: 
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1gwuserviceReplicas.PNG)
  * Now run ```docker network inspect overnet```. We can find it is in the container with ip address "10.0.0.7" running on the node1 and "10.0.0.8" on the node2.
* Test the network
  * First we need to log on the service task by ```docker exec -it dockerID /bin/bash```. (I load to the gwuservice node2 which has ip 10.0.0.8 here)
  * Install the ping program and ping it again ```ping -c5 10.0.0.8``` and we can find that both task from the gwuservice are on the same overlay network.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1node2ping.PNG)
* Test the service discovery.
  * Run ```cat /etc/resolv.conf``` to get the "nameserver 127.0.0.11". This value will sends all DNS quries from container to an embedded DNS resolver running inside the container listening on 127.0.0.11:53
  * The container is also be able to ping the gwuservice by name and notice that the value returned from ping is the same as the gwuservice vip(virtual ip address).  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1vip.PNG)
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/1vipping.PNG)
* finally clean up all.

> Summary: 
   * The network performance inside and outside of the container is basically no difference. After comparing pinging "github.com" both inside and outside, the RTT only is different in 0.001 ms.
   * There are many kind of driver for containers networking such as bridge(default) and overlay and they are scoped in local and swarm respectively. Generally speaking, the bridge is created for connection and the overlay can be used to create a virtual network between sepearate host even itself and it is build over an existing netwrok. May be this is the reason why it is called overnet.
   * There are many powerful commands for inspecting and managing network but since we can create many containers in a OS, we should still care about the management which large scale interconnect in my opinion. (After writing up this summary, I found the swarm solution in next section. Lucky anyway!)
 
<a name ="swarm"></a>  
### Swarm Mode Introduction Lab
[Back to Menu](#menu)
> Foreword  
  * Docker Swarm is used to cluster and schedule Docker containers. More details, Swarm Mode provides both the ability to define application architecture and to maintain high availability levels, scaling and load balancing.
  * In last section, I just worry about too many containers will cause management problem and the Docker Swarm Mode introduced in this section will be a good solution.
  * The Compose Mode (not used in this section) is also a useful text file that describes the application: which images to use, how many instances, the network connections, etc and it is run on a single system.


* Initialize Your Swarm
  * As we did in last section, use ```docker swarm init --advertise-addr $(hostname -i)``` to initialize Docker Swarm Mode manager and join the worker node to swarm by ```docker swarm join -token ...``` (There are 2 node terminal, run manager and worker commands respectively).
  * Now, we have one manager and one worker.  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2twonodes.PNG)

* Show Swarm Members
  * Show the swarm members with command ```docker node ls```. There is a node1 whose MANAGER STATUS is Leader due to it is the first manager in the node list and if this node go down for some reasons, the other managers will elect a new leader.
  * There is a view of the Swarm architecture:  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2swarmArch.PNG)
  * As you can see, only the first manager is the leader and there are a bunch of managers(also can be worker) and workers(can not be manager unless explicitly promoted). They both run service containers and manager nodes can coordinate the cluster quorum. All docker stack and docker service commands must be run from a manager node.

* Clone the Voting App
  * use git clone --depth 1 ```git clone --depth 1 https://github.com/docker/example-voting-app``` to get the voting app material.
  * go to it's directory by ```cd example-voting-app```

* Deploy a Stack
  * A stack is a group of services that are deployed together. The components of a single App can run in separate instances(containers) which is called tasks and all tasks&service make up stack.
  * The "docker-stack.yml" is the plain text file used to define a stack (architecture of services, number of instances, how everything is wired together and how to handle updates to each service). We can check it by ```cat docker-stack.yml```.
  * Format:  
    Service name  
    How the service run(image for particular service, ports and networks and labeld replicas)  
    ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2ymlexample.PNG)
  * Deploy it by ```docker stack deploy --compose-file=docker-stack.yml voting_stack```. In the voting App we have 6 service and we can check it by ```docker stack ls``` and got the detail of each stack by ```docker stack services voting_stack```.
  * We can run the ```docker service ps voting_stack_vote``` to get the task running for each node. This Apps has a built-in SWARM VISUALIZER to show it is setup and running.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/votingstack.PNG)  
  * Here are the front-end web UI of the Application  
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2frontend.PNG)
  * In this architecture, Swarm has three component:
    * Stack: It is a group of interrelated services and dependencies and is orchestrated as a unit.
    * Tasks: Atomic unit of a service and scheduling in Docker. One container per task!
    * Services: Including a container image, number of replicas(tasks), ports and update policy.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/SSTarch.PNG)
* More about Stack:
  * Stack is a multi-service app running on a Swarm, a way of creating mutiple nodes and a method of using mutiple compose files to run an app.
  * It can:
    * be deployed from the commandline.
    * use the compose file format to deploy
    * run a Dockerfile
    * be used to manage your hosts
    * be used to manage services over multiple nodes.
   
  

* Scaling an Application
   * Use ```docker service scale voting_stack_vote=5``` to change the number of vote service to 5 adn use ```docker services voting_stack``` to check it.
   * SWARM VISUALIZER - from 2 workers to 5 workers.
   ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2addworker.PNG)
   ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2votingTable.PNG)
   ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2update%20service.PNG)
   * After scaling the voting_stack_vote at node2, the number of tasks in vote of voting_stack_node2 increase to 5.
   ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/2afterstacjArch.PNG)

> Summary:
  * Swarm is an architecture for node(container) management, and the manager nodes allow running the docker stack and service commands. For instance, we run stack and service command to configure the voting app in the node 1 in the lab and node2 is just a worker joined in the swarm. With the manager node, we can add vote, display front-end interface and so on. In the more higher level, we have stack which is a multi-service app running on the Swarm architecture which allows us to manage all the nodes and it should be through the manager.

<a name ="swarmkuber"></a>
### Docker Swarm vs Kubernetes and More about Kubernetes.
[Back to Menu](#menu)
> Docker Swarm vs Kubernetes
* Solutions for orchestrating containers: Kubernetes, Mesos and Docker Swarm -> for providing an abstraction to make a cluster of machines behave like one big machine, which is vital in a large-scale environment.
* Docker Swarm: It is a scheduler provides a way to adminster a large number of containers spread across clusters of servers which enables the selection of optimal nodes in a cluster to deploy containers.
* Kubernetes: It is an open source container orchestrator and a comprehensive system for automating deployment, scheduling and scaling of containerized applications, and supports many containerization tools such as Docker.

> Official doc for [Kbernetes](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
* Kubernetes coordinates a highly available cluster of computers that are connected to work as a single unit. It automate the distribution and scheduling of application containers across a cluster in a more efficient way.
  * The Master coordinates the cluster
  * Nodes are the workers that run applications.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/3kuberdigram.PNG)
* Cluster Digram:
  - Bullet point: As we learn in the Docker Swarm which has manager and worker(nodes), the Kuberdigram also has the Master(used to manage and communicate with Node) and workers(Nodes). 
  * The Master is responsible for manging the cluster.
  * A node is a VM or a physical computer that serves as a worker machine in a Kubernetes cluster. (Kubelet communicate with the Kubernetes) The nodes communicate with the master using the Kubernetes API.
* Create a Cluster
  * Use the ```minikube start``` to start the local Kubernete cluster and VM. Then use ```kubectl version``` to check the client and server version.
  * Use ```kubectl cluster-info``` to get the running environment IP address and port number and use ```kubectl get nodes``` to view the nodes in the cluster.
  
* Kubernetes Deployments
  - Bullet point: Deploy the kubectl interface on the Node whcih can create Deployments that run applications on a kubenetes cluster.
  * Deploy the containerized application on top of Kubernetes cluster and use self-healing mechanism to address machine failure or maintenance.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/3deploy.PNG)
  * Create and manage a Deployment by ```Kubectl```. Run the app on Kubernetes by ```kubectl run``` which create a new deployment and we can add the ```--port=1234``` to let the app run on a specific port. 
  * Pods that are running inside Kubernetes are running on a private, isolated network. (jump host by ```kubectl proxy``` command)
  * First we can query the version of a host with ```curl host:port/version``` and then we can set and get the Pod name by ```export``` and ```echo```

* Explore the APP
  - Bullet point: Each Pod is a "logical host" and containers in a Pod share the same node, IP address and Port space.
  * After deployment, Kubernetes create a Pod to host the application instance. A Pod is a Kubernetes abstraction that represents a group of one or more application containers. Some of containers share resources (Storage-volumes, networking and running information).
  * A Pod runs on a Node which is a worker machine in Kubernetes and run at least 1.Kubelet (communication process) and 2. A container which is responsible for pulling the container image from a registry, unpacking the container and running the application.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/3nodeoverview.PNG)
  * Some commands: ```kubectk get pods```, ```kubectl describe pods```, retrive STDOUT logs using ```kubectl logs $POD_NAME``` and execute command directly on the container using ```kubectl exec $POD_NAME```.

* APP Publicly
  * Bullet point: Label is used to determine the services of the set of Pods.
  * There are many services for the management of Pods and it is usually determined by *LableSelector.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/3servicelabel.PNG)
  * Labels are key/value pairs attached to objects and can be used in:
    * Designate objects for development, test and production
    * Embed version tags
    * Classify an object using tags
  * Apply a new label to Pod by ```kubectl label pod $POD_NAME app=v1```.
  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/3seelabel.PNG)

* Scale App and Rolling Update
  * Bullet point: Scaling is also a requirement for performing updates and rolling update allows the integrate and deliver the application with zero downtime. 
  * Scale Down the app by ```kubectl scale ...``` command. Scaling out a Deployment will ensure new Pods are created and scheduled to Nodes with available resources.
  * Rolling Update allow Deployments' update to take place with zero downtime by incrementally updating Pods instances with new one.
  * Procedure:
    1. add a new container with new IP address as the uodated app. (basic step)
    2. do it one by one in other nodes and finally update all nodes. (1. Promote an application from one environment to another via contaier image updates, 2. Rollback to previous versions and 3. Continuous Integration and Continuous Delivery of applications with zero downtime.
  * Example Procedure:
    * Update the version of app by ```kubectl set image ...```.
    * Verify an update by ```kubectl rollout status``` or ```curl $(minikube ip):$NODE_PORT```.
    * Rollback an update by ```kubectl rollout undo``` which can reverte the deployment to the previous known state.
> Summary: 
  * Both Docker Swarm and Kubernetes are clustering solution for Docker containers and Kubernetes is the market leader but it needs to run with docker containers.
  * Kubernetes is more automatic with the Pod and policy and some managing API, you can easily deploy, scale and update your App built on the container inside Node(Pod) in the kubernetes environment.
  

   

<a name ="breakservice"></a>
### Break a Monolith Application into Microservices
[Back to Menu](#menu)
* Service Architecture:
  1. Monolithic Architecture: The entire node.js application is run in a container as a single service and each container has the same features as all other containers. If one application feature experiences a spike in demand, the entire architecture must be scaled.
  2. Microservices Architecture: Each feature of the node.js application runs as a separate service within its own container. The services can scale and be updated independently of the others.
  
> Step1: Preparing.
* Install docker on my Windows: need to enable the virtualization service on BIOS mode.
* Setup aws by ```pip install awscli --upgrade --user``` and add the python path to System Path with ```set PATH%PATH%;C:\...```
* Login in to AWS Authenticate Docker with command ```aws ecr get-login --no-include-email --region us-east-1``` and my "AWS Access Keu ID" and "AWS Secret Access Key" in the "credential.csv".
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4successlogin.PNG)
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4successlogin2.PNG)
* Then biuld the image with ```docker build -t api .```. However, there is a bug in the lab instruction and you should first ```docker login``` then run this command.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4buildimage.PNG)
* After that tag the image to push with command ```docker tag api:latest [account-id].dkr.ecr.[region].amazonaws.com/api:v1```. And push it to ECR with command ```docker push [account-id].dkr.ecr.[region].amazonaws.com/api:latest```. In this place, you should copy the repository url rather than using the instruction format.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4pushimage.PNG)

> Step2: Deploy the Monolith
* Architecture Overview: 
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4monolitharch.PNG)
   * Client makes a request over port 80 to the load balancer -> The load balancer distributes requests across all available ports -> Instances are registrered in the application's target group -> Each container runs a single application process which binds the node.js cluster parent to port 80 within its namespace -> The node.js cluster parent is responsible for distributing traffic to the workers within the monlithic application. (monolithic because each container has all features of the rest of the containers.)

* Amazon ECS(Elastic Container Service): a good container managerment service allows you to run Apps on a managed cluster of Amazon EC2 instances.
* Create an AWS CloudFormation Stack and upload the "ecs.yml" file in containerized-infrastructure folder as a template
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4ecscluster.PNG)
* Next we will write a task definition named "api" with the image we build previously and use the load balancer(ALB) in EC2 management to see the VPC and use it to create the ALB target group. And then add a Listener(in EC2 Load Balance) and set Forward to "api".
* Finally we will deploy the Monolith as a service by configuring the service in Amazon ECS console.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4myService.PNG)
* Summary: After pushing the tagged image onto the repository, we need to create a target group (with VPC) to regestry the instances of it and set a listener of it. Finally we can deploy it with all things linked. 


> Step3: Break the Monolith and Deploy Microservices
* Bullet points: Microservice provides more isolation and allows more tolerance to crashes handling and more security. At the same time, it also allows scale independtly and develops faster.
* Architecture Overview  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4breakarch.PNG)  
   * Client makes traffic requests over port 80. -> The ALB routes exterbak traffic to the correct service and inspects the client request and uses the routing rules to direct the request to an instance and port for the target group matching the rule. -> Each service has a target group that keeps track of the instance and ports of each container running for that service. -> EC2 deploys each service into a container across an EC2 cluster. Each container only handles a single feature.

* Provision The ECR Repository: Create three repository -users, threads and posts.
* Then build an push images for each service. (be careful the ID is the actual ID)
* Deploy Microservices:  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4deployMicro.PNG)
* With the Monolith task definition experience, I find that json deployment is much easier. After the definition, we create three target groups correspond to it and one more "drop-traffic" group which is a 'dummy' target (using to keep traffic from reaching your monolith after your microservices are fully running).
* Then we will configure the Listener (checking for incomming connection requests to your ALB in order to route traffic appropriately) rules again, and Deploy all of it and turn off "api"  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4afterturnoff.PNG)
* The users result (validation, same as threads and posts):  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/4users.PNG)

> Summary:
   * Due to unfamiliar with AWS services such as Load Balanacer and Clusters, I paid lots of time to do it again and again but it is meaningful. I got the skills of json configuration, awscli and microservices deployment now.
   * In a cluster, we can set one container or multiple containers with different image. The strengths of multiple microservices are that you can easily adjust each unit, no matter turn on/off, update or duplicate. Furthermore, it is more reliable since they are isolated and the whole system may not be influenced even if some of them are crashed.
   * With AWS, we can easily divied the monolith program into multiple small service and its user-friendly interface lets us only need to consider how to spilt the monolith service into microservices.
**********************************

# Cloud Web Application

<a name ="launchvm"></a>
### Launch a VM
[Back to Menu](#menu)
> Foreword: In this section, I will use AWS EC2 to build a VM Instance, keep it's Keypair and link to it.
* step 1: Launch an Amazon EC2 Instance. In this step, we need to remember to create a new key pair and move it to .ssh path on mac. After instance launched, we can view its details at the EC2 Instance page.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/5instance.png)
* step 2(optional): this step is not in the tutorial, however, I found that there is a permission denied with error "Permissions 0644 for '/Users/unlimitediw/.ssh/MyKeyPair.pem' are too open." and it is due to EC2 instances will not accept a .pem key if it is publicly visible. I figure this problem with two commands ```chmod 400``` and ```chmod 400 ~/MyKeyPair.pem``` which give the keypair a more security and appropriate setting.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/5chmod.png)
* STEP 3: Link to the VM instance we just lanuched with command ```/Users/unlimitediw/.ssh/MyKeyPair.pem' ec2-user@54.210.136.241``` and it is validated.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/5validate.png)

> Summary: EC2 service gives allows us to build a Linux VM service conviently and we should use a set of keypairs to manage the entrances of it and keep it safe. In the future work, I may use it to do some small Linux env needed lab with my windows or mac laptop and it is time saving.

<a name="wp3"></a>
# Introduction to Amazon Simple Storage Service (S3) (30mins) 
[Back to Menu](#menu)
### Task 1-3: 
1. Create bucket.
2. Upload image file. 
3. Open the read access manually.
### Task 4: Create a Bucket Policy
    Use AWS Policy Generator to generate S3 Bucket.
    Select S3 Bucket Policy, write ‘*’ in Principal and paste ARN (Amazon Resource Name).
    The policy looks like:
    {
      "Id": "Policy***************",
      "Version": "2012-10-17",
      "Statement": [
            {
              "Sid": "Stmt*************",
              "Action": [
                "s3:GetObject"
              ],
              "Effect": "Allow",
              "Resource": "arn:aws:s3:::********(bucket name)/*",
              "Principal": "*"
            }
        ]
    }
    After generation of policy and being applied in the Bucket Policy, the bucket is public accessible now.
### Task 5: Explore Versioning:
    Add action “s3:GetObjectVersion” in “Action” will allow access old version of a file.

> Summary: S3 is very easy to use and is a Object Storage service. In my Round 1 lab, all my img link is provided by AWS S3 and I can easily manage it with the S3 bucket.   
****************************************************
<a name ="mysagemaker"></a>
# Build a Machine Learning Model by SageMaker
[Back to Menu](#menu)

> Today I will talk about Amazon Sagemaker - a fully-managed machine learning service (announced in Nov 2017) and will mainly focus on two point: why we need it and how it works.

## What I care most?
Machine learning is a method of data analysis that automates analytical model building and it is become more and more popular in recent years. Nowadays, even my own laptop can build and train some simple machine learning models such as MLP and SVM. However, when dealing with deep neural networks, real time streaming processing or large bunch of data, it will kill lots of time and make my laptop crashed.  
  
To solve this problem, I would like to use some distributed system techniques like EC2 Instance for computing, S3 for storage and Microservices for deployment but it is still a hard work to organize all of it.  
  
Fortunately, Amazon provides us a solution SageMaker combining all of these techniques and optimizing in each step.

## SageMaker Components
> Amazon SageMaker is a fully managed service for machine learning which includes the processes of data preparing, model training and model deployment. 
* Build (data preparing): SageMaker supports Jupyter notebook and allows you to transform data and connect to other AWS services such as S3 in this part. 
* Train (tune): In machine learning, after constructing the model, we also need to get the optimize hyperparameter with the works such as SVM or Adaboost. SageMaker provides the One-click distributed training with built-in model and can train the parameters in large scale which will improve the speed and accuracy (more iteration).
* Deployment: It is an amazing Endpoint job which allows individual such as me, a student, to show my own work such as map generator and image recognition model on the website and may allows me to apply it for commercial use in the future.

## SageMaker Procedure
> The data preprocessing parts such as labeling are well defined by Amazon SageMaker. Nevertheless, it is more like a repetition laboured work such as human image classification, handwork bounding box drawing and so on.  

> Create a Jupyter Notebook Instance

![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6myInstance.PNG)
* After instance created, you can choose the built-in notebook environment such as the Sparkmagic for streaming data processing or my most love one, the conda tensorflow which work very well in the convolutional neural network training.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6sparkenv.PNG)  ![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6tensorenv.PNG)
* Now we can edit and build our own machine learning model on Jupyter Notebook (build in SageMaker notebook). The highlight of it I believe is that I do not need to worry about the problem of environment setting (due to Amazon familiars with their machine, they are more easier to apply environment on their hardware than us) and many open-source algorithm is built by amazon machine learning experts which can be easily modified and directly used on the SageMaker Notebook Instance.
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6notebookedit.PNG)
> Train the model built in the Jupyter Notebook Instance.
* After training, we will get the model as follow. I am a little bit disappoint in this part since there is no to much difference with the training process on my own laptop. However, Amazon Segamaker will provide the log file of training to you which is helpful (in this model, it provides the xgboost prunning details for the decision tree model).
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6sagemaker_train.PNG)
* The trained model is also saved in the instance  
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6mymodel.PNG)

> Create the Endpoint
* Create the endpoint by the API ```sm.create_endpoint(EndpointName = endpoint_name, EndpointConfigName = endpoint_config_name)```
* Deploy the model by  ```xgb_predictor = xgb.deploy(initial_instance_count=1, instance_type='ml.t2.medium')```
* After this, we can find the endpoint at the Endpoint configuration section
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6endpointConfig.PNG)
* This is the endpoint details
![](https://github.com/unlimitediw/DistributedSystemLearn/blob/master/Image/6endpointdetails.PNG)

## Special topic I - Using the tf.estimator
> SageMaker support Google TensorFlow very well with the ```sagemaker.tensorflow.TensorFlow``` estimator class. You can use the ```fit ``` API to train your deep neural network model and ```deploy``` API to deploy the resulting model on Amazon SageMaker.
* When the Sagemaker Instance is training model, several other parts of AWS services are workin at the same time:
  1. The Docker image which contains the TensorFlow framework will be load by SageMaker.
  2. S3 bucket will provide the training data as an object to the SageMaker container's file system.
  3. tf.estimator will use the ```DNNClassifier``` API to constructs a neural network model.
* The remaining parts of Deployment is the same as the previous section.

## Special topic II - Amazon SageMaker Inference Pipelines
> SageMaker Inference Pipeline is high related to my Round 2 topic: containers and microservices. This pipeline allows containers to be deployed in an inference pipeline that are co-located on the same EC2 instance.
* Although we may use multi services in the deployment of our machine learning model. We can still get a low lattency performance with this technique since you specify the order in which the containers are executed when creating the inference pipeline model. You can also update this endpoint process by redeploying later. 

* Here is an example for the [Inference Pipeline](https://github.com/awslabs/amazon-sagemaker-examples/blob/master/advanced_functionality/inference_pipeline_sparkml_xgboost_abalone/inference_pipeline_sparkml_xgboost_abalone.ipynb)

## Summary
- SageMaker is an amazing work due to it's easy operation and high performance. When I applied the xgboost model from data preoprocessing to training to deploying, I only took several one-click works with the prebuilt python codes. It is very meaningful to data scientist because in most situation they need more chances to test their thought, their algorithm and model. There is no standard line in machine learning, trying more means perform better at most of the time. Moreover, The super high performance for some instance such as "ml.p3" is also essential since it means you don't need to wait. Even the price per hour is high, you spend less time and it can help you validate your "inspiration" immediately.
- SageMaker, with a bunch of distributed system techniques such as EC2 and containers, give us a chance to realize our machine learning assumption. To me, I can use it to train my own "Map Generation" CNN based Model and display(deploy) the result on my personal website. And for my another project of real time video recognition, the high performance of spark streaming processing also boosts my program value. No matter to individual, a lab team or a coporation, SageMaker provides them all a chance to applied their academic machine learning code for pratical usage and amplify its performance.

****************************************************
# BIG DATA AND MACHINE LEARNING

<a name="Introduction"></a>
# Hadoop Introduction (2h)
[Back to Menu](#menu)
### Overgrowing data problem and management:
- High chances of system failure.
- Limit on bandwidth.
- High programming complexity.

Hadoop is a framework that allows for distributed processing of large data sets across clusters of commodity computers using simple programming models.

### Hadoop key characteristics:
- Economical
Ordinary computers can be used for data processing.
- Reliable
Stores copies of the data on different machines and is resistant to hardware. failure
- Scalable
Can follow both horizontal and vertical scaling.
- Flexible
Can store as much of the data and decide to use it later.

    In my project, I may not need to use Hadoop for a single video processing. However, if I need to deal with large amount of concurrent image data, I need the Hadoop with such for important characteristics.

### Traditional Database System vs. Hadoop
- Tradition Database System:
	1.	Data store in central location.
	2.	Sent data to processor at run time.

    Works well with limited data. However modern systems receive terabytes of data per day and it is difficult for the traditional computer or relational database management system such as RDBMS-Relational Database Management System.
- Hadoop:
	1.	Program goes to the data.
	2.	It initially distributes the data to multiple systems and later runs the computation whenever the data is located.

### Hadoop components
- Hadoop Ecosystem core components
	1.	Processing.
	2.	Resource management.
	3.	Storage.

- Data processing:
    * Hadoop Distributed file system (HDFS):
    	A storage layer of Hadoop.
    	Suitable for the distributed storage and processing.
    	Hadoop provides a command line interface to interact with HDFS Streaming access to file system data.
    	Provides file permissions and authentication
    * Hadoop map reduce:
    	The original Hadoop processing which is primarily Java based
    	Based on the map and reduce programming model Commonly used
    	An extensive and mature fault tolerance framework
    * Spark:
    Instead of storage data, Spark optimizes dealing with consistent data stream.
    An open-source cluster computing framework
    Provides 100 times faster performance as compared to MapReduce
    Support Machine learning, Business intelligence, Streaming, and 
    Batch processing
    Apache Spark
		* Spark Core and Resilient Distributed Database (RDDs)
		* Spark SQL
		* Spark Streaming
		* Machine Learning Library (Mlib)
		* GraphX
* Data Ingestion:
	* Sqoop:
Sqoop is a tool designed to transfer data between Hadoop and relational database servers
It is used to import data from relational databases such as, Oracle and MySQL to HDFS and export data from HDFS to relational databases.
    * Flume:
	A distributed service for ingesting streaming data.
	Ideally suited for event data from multiple systems.
* Data Analysis:
	* Pig:
		An open-source dataflow system.
		Converts pig script to Map-Reduce code.
		An alternate to writing Map-Reduce code.
		Best for ad-hoc queries like join and filter.
    * Impala:
    	High performance SQL engine which runs on Hadoop cluster.
    	Ideal for interactive analysis.
    	Very low latency – measured in milliseconds.
    	Supports a dialect of SQL (Impala SQL).
    * Hive:
    	Similar to Impala.
    	Best for data processing and ETL (extract, transformation, load).
    	Executes queries using MapReduce.
* Data Exploration:
	* Cloudera Search:
        One of Cloudera’s near-real-time access products.
        Users do not need SQL or programming skills to use Cloudera Search.
        Enables non-technical users to search and explore data stored in or ingested into Hadoop and HBase.
A fully integrated data processing platform.
    * Hue:
        Hue is an acronym for Hadoop User Experience
        Hue is an open source Web interface for analyzing data with Hadoop
        It provides SQL editors for Hive, Impala, MySQL, Oracle, PostgreSQL, Spark SQL, and SolrSQL.
* Workflow System:
	* Oozie:
		Oozie is a workflow or coordination system used to manage the 
		Hadoop jobs
####
    		Start
    			Loop actions
    				Oozie Coordinator Engine
    					Oozie Workflow Engine
    		End
* NoSQL:
	* HBase:
        Store data in HDFS.
        A NoSQL database or non-relational database Mainly used when you need random, real time, read/write access to your Big data
        Provides support to high volume of data and high throughput
        The table can have thousands of columns.

<a name="bigdata"></a>
# Analyze Big Data with Hadoop (1h)
[Back to Menu](#menu)

### Task 1: Create an Amazon S3 bucket
* What is S3? Simple Storage Service is designed to make web-scale computing easier for developers. In my opinion, simple means that we can have more time to deal with other hard tasks and boost the probability of finish a project entirely.
* In this task, I will create a bucket to store my log files and output data.
(P.S. log file is a file that records either events that occur in an operating system or other software runs, or messages between different users of a communication software.
* In my works, I will use some image label tools to process the uploading image frame and take parts of it with high confidence as training data and store them in S3 bucket. I may also store the client information and log files into the S3 bucket
### Task2: Launch an Amazon EMR (Elastic MapReduce) cluster
* Introduction:
EMR provides a managed Hadoop framework that makes it easy, fast and cost-effective to process vast amounts of data across dynamically scalable Amazon EC2 instances. EMR is a data processing tool with a broad set of big data use cases, including log analysis, web indexing, data transformations (ETL), machine learning, financial analysis, scientific simulation, and bioinformatics.
* In EMR, you can run custom Map reduce code, Spark (data processing) or frameworks such as Hive, Pig, Impala (data analysis) and HBase (NoSQL), Apache Hadoop, Ganglia, Apache Tez.
* User can build as many clusters as they need to test new ideas and terminate the clusters when they’re no longer needed. In my project, I may build several EMR with several different CNN models and test their real time image processing ability at the same time and take a comparison at the same time.
* For security demand, you can launch clusters in an VPC (Virtual Private Cloud), a logically isolated network that you define.
* Data to be Processed:
Use cluster to process log data from Amazon CloudFront which is a web service that speeds up distribution of static and dynamic web content such as .html, .css, .php, and image files.
* In this task, log data contains: Date, Time, Edge Location, Bytes, IP, Method, Host, URI (uniform resource identifier), Status, Referrer, User Agent
### Task3: Process your sample data by running a hive script
* Hive is a data analysis tool which is best for data processing and ETL.
* There are three steps for the hive operation:
    1. Create table. 
    2. Use regex to read log file. 
    3. Use HiveQL query to calculate requests by operating system.
Finally, it will give us a operating system counting table
### Task4&5: view result and terminate amazon EMR Cluster 

<a name="s3"></a>
# Introduction to Amazon Simple Storage Service (S3) (30mins) 
[Back to Menu](#menu)
### Task 1-3: 
1. Create bucket.
2. Upload image file. 
3. Open the read access manually.
### Task 4: Create a Bucket Policy
    Use AWS Policy Generator to generate S3 Bucket.
    Select S3 Bucket Policy, write ‘*’ in Principal and paste ARN (Amazon Resource Name).
    The policy looks like:
    {
      "Id": "Policy***************",
      "Version": "2012-10-17",
      "Statement": [
            {
              "Sid": "Stmt*************",
              "Action": [
                "s3:GetObject"
              ],
              "Effect": "Allow",
              "Resource": "arn:aws:s3:::********(bucket name)/*",
              "Principal": "*"
            }
        ]
    }
    After generation of policy and being applied in the Bucket Policy, the bucket is public accessible now.
### Task 5: Explore Versioning:
    Add action “s3:GetObjectVersion” in “Action” will allow access old version of a file.

<a name="redshift"></a> 
# Introduction to Amazon Redshift (1h 30mins)
[Back to Menu](#menu)
* Amazon Redshift is a data warehouse to analyze data using standard SQL and existing Business Intelligence tools.
### Task 1: Launch an Amazon Redshift Cluster
* Launch cluster to open the Redshift Cluster Creation Wizard
(Optional: node type, control encryption, VPC)
* Cluster Properties: Contains information about the Cluster including: Name, Type, Node Type, number of Nodes, Zone location, Time and version of creation as well as other information
<!-- Tables -->
| Cluster Props   | value           |
|-----------------|-----------------|
|Cluster Name     |lab              |
|Cluster Type     |Single Noed      |
|Node type        |dc2.large        |
|Nodes            |1                |
|Zone             |us-east-1e       |
|Created Time     |*                |
|Maintenance Track|Current          |
|VPC ID           |vpc-*            |
|VPC sec groups   |defa.redshift-1.0|
|Cluster Par Group|lab              |
|Cluster Name     |lab              |
|Cluster Name     |lab              |
* Cluster Status: Allows you to see the current status of the cluster whether it is available or not and also whether it is currently in Maintenance Mode
* Cluster Database Properties: Contains information on the Endpoint, which is the DNS address of the cluster, and the port number on which the database accepts connections. These are required when you want to create SQL connections. It also lets you know whether the cluster has a public IP address that can be accessed from the public internet. The JDBC URL and ODBC URL contain the URLs to connect to the cluster via a java database connection or an Oracle database connection client.
<!-- Tables -->
| Cluster DB Props| value           |
|-----------------|-----------------|
|Port             |1234             |
|Publicly Access  |Yes              |
|Database Name    |labdb            |
|Master Usernaem  |master           |
|Encrypted        |No               |
|JDBC URL         |jdbc:redshift://~|
|ODBC URL         |Driver={};       |
|ODBC URL         |Server=~         |
|ODBC URL         |DB=labdb;UID     |
|ODBC URL         |PWD=pwd;Port=1234|
* Backup, Audit Logging and Maintenance: Contains information on how many days the automaated snapshots are retaubedm whether they are automatically copied to another region, and whether logging is enabled on the cluster.
* Capacity Details: Contains information about the data ware house node type, number of EC2 Compute Units per node, memory, disk storage, I/O performance as well as the processor architecture of the node type.
* SSH Ingestion Settings: Contains information about the Public Key of the cluster as well as the Public and Private IP addresses of the node.

### Task2-3: Launch Pgweb to Communicate with Redshift cluster and create a Table
* Any SQL client that supports JDBC or ODBC can be used with Redshift. In this task, Pgweb which provides a friendly SQL interface to Redshift will be used.
* Use redshift cluster endpoint as host and port and connect on Pgweb.
* Then use SQL command to create a table in Redshift which contains name, address and details about the type of music that user likes.

### Task 4-5: Load Sample Data from Amazon S3 and Data processing
* Amazon Redshift can import data from Amazon S3. Various file formats are supported, fiexd-length fields, comma-separated values(CSV) and custom delimiters. The data for this lab is pipe-separated.
* Use SQL Query to load and show Data in Redshift.
* Analyze data with Query.

<a name="introml"></a> 
# Intro to Amazon Machine Learning (1h 30mins)
[Back to Menu](#menu)
### Task 1: Uploading Training Data
* Upload restaurant.data to a S3 bucket
* Features in order: age, gender, budget, price, cuisine_type, rating

### Task 2-3: Create a Datasouce
* Does the first line in your CSV contain the column names? Yes, which is the feature names in order.
* Use "age, gender, budget, price, cuisine_type" as input data set and "rating" as label.
* AWS also allow custom ML model with recipe.

### Task 4: Evaluate an ML Model
* In the learning task, 70% of data is used to training and 30% of data is used to validate and test.
* The AWS provided ML model is multclass classification.
* The result will be visualized by confusion matrix. In the confusion matrix, the rows represent the true values and the columns represent the predicted values. 
![](https://s3.amazonaws.com/data357/ConfusionMatrix.PNG)

<a name="buildml"></a> 
# Build a Machine Learning Model (1h 15mins)
[Back to Menu](#menu)

### Task 1: Prepare your data
* Data Resource: [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/index.php)
* Data: 
	* banking.csv (used for training and validation)
	* banking-batch.csv (used for test)
* Features:
age | job | marital | education | default | housing | loan | contact | month | day_of_week | duration | campaign | pdays | previous | poutcome | emp_var_rate | cons_price_idx | cons_conf_idx | euribor3m | nr_employed
* Label: Y = [0,1]

### Task 2-3: Create a Training Datasource and Create an ML Model
* Use [Amazon Machine Learning](https://console.aws.amazon.com/machinelearning/.) 
* Link the input data with banking.csv which is uploaded in S3 bucket
* There are three types of data: Numeric, Categorical, Binary
* Use a binary classification model

### Task 4: Review the ML Model's Predictive Performance and Set a Score Threshold
* During evaluation, Amazon ML computed an industry-standard quality metric, called the Area Under a Curve (AUC) metric, that expresses the performance quality of your ML model.
* Review the AUC metric:
	* In the label set, 11% of it is 'yes' and the other is 'no'.
	* In our prediction, True positive rate is 4% and false positive rate is 1.8%, which means that the precision is 69.0%. At the same time, the false nengative rate is 7% which means that the recall rate is 36.4%.
	* In the score threshold model, the prediction is based on the cut-off number. Although I can not see the algorithm inside AWS calssification model, it is believable that this image shows the distribution of data base on their prediction values with sigmoid function.
	![](https://s3.amazonaws.com/hadoop357/explore-screenshot-v1.png)
	* Generally speaking, the score setting is based on the client demand. If they have a lots of money and is willing to invest with high risk may be they can select a lower score to reduce the false negative rate but increase true positive rate while maintain the accuracy in a acceptable level.
	![](https://s3.amazonaws.com/hadoop357/eva.PNG)
	
### Task 5: Use the model
* record format: comma spilt
	* 32,services,divorced,basic.9y,no,unknown,yes,cellular,dec,mon,110,1,11,0,nonexistent,-1.8,94.465,-36.1,0.883,5228.1
* Real-time predictions will give us the prediction result directly
* The cost of batch prediction is 0.1$/1000prediction. To be honest, the model construction and prediction for 42000 messages will only takes few minutes on my computer. We should be care about it since it is expensive.
* There are three columns of the batch prediction result: trueLabel, bestAnswer and score. It is due to that I select the banking.csv to batch predict.
* In my model, the score larger than 0.35 will be taken as 1 and the score smaller or equal than 0.35 will be taken as 0.

<a name="viewsegmaker"></a> 
# AWS Overviwe of SageMaker (1h 30mins)
[Back to Menu](#menu)
* The most important things I believe is that you can use your own scripts to train your model.
### What is SageMaker?
* Amazon SageMaker enables you to build, train, and deploy machine learning models quickly and easily while taking care of the heavy lifting of machine learning.
	* Build: Provides hosted Jupyter notebooks that require no setup and allows to start processing the training dataset & developing algorithms immediately.
	* Train: One-click, on-demand distributed training with built-in model tuning that can automatically adjust hundreds of different combinations of algorithm parameters for greater, speed, accuracy, and data-throughput.
	* Deploy: An elastic, secure, and scalable environment to host your models, with one-click deployment.
### Overview
* Notebook instance: Explore AWS data in your notebooks, and use algorithms to create models via training jobs.
* Jobs(training): Track training jobs at your desk or remotely. Leverage high-performance AWS algorithms.
* Models(host and repository)
: Create models for hosting from job outputs, or import externally trained models into Amazon SegaMaker.
* Endpoint(deploy): Deploy endpoints for developers to use in production. A/B Test model variants via an endpoint.
### SageMaker tutorial
* Create notebook instance
	* There may have some bugs if Data and Instance not live in the same region in a S3 bucket.
* e.g Training the XGBoost model
	* Images hosted in different regions.
	* ResourceConfig: size of the training instance used to deploy model
	* HyperParameters: such as "max_depth", "eta", "gamma" etc.
		* p.s You call something a 'hyperparameter' if it cannot be learned within the estimator directly. 
	* Use API to train single or multiple instances.
		* e.g sm.create_training_job(**single_machine_job_params)
* In the job menu, you will see the progress information here.
* Then hosted the model in SageMaker for prediction and it is hosted in a container.
	* Grab some information from train model.
	* Build endpoint configuration.
	* You can also build multiple model to do the a/b testing("two-sample hypothesis testing).
* Test and get error rate and confusion matrix.
* You can also use model trained outside in SageMaker.
* After laiding all files, scripts and libraries you need to write a docker file and build a container.
* Create repository and push container to ECR(Elastic Container Registry). 
	
	

<a name="segmaker"></a> 
# AWS SageMaker (2h)
[Back to Menu](#menu)
* I lost my .md file in this episode, so the following introduction will be more concise.
### Getting Started
* Create an S3 bucket a usual.
* Create an SageMaker Notebook Instance
* Train Model and Deploy it
* p.s Some important image I used before this episode lost:
	* SageMakerLoop	
		![](https://s3.amazonaws.com/hadoop357/SageMakerLoop.PNG)
	* BatchTransform
		![](https://s3.amazonaws.com/hadoop357/batchTransform.PNG)

<a name="app"></a> 
# Build a Serverless Real-Time Data Processing APP (6h)
[Back to Menu](#menu)
### Overview
* In this project, I’ll build a serverless app to process real-time data streams and build infrastructure for a fictional ride-sharing company. In this case, I will enable operations personnel at a fictional Wild Rydes headquarters to monitor the health and status of their unicorn fleet. Each unicorn is equipped with a sensor that reports its location and vital signs.

### Procedure
* Build a data stream
	* Create a stream in Kinesis and write to and read from the stream to track Wild Rydes unicorns on the live map.
	* create an Amazon Cognito identity pool to grant live map access to your stream.
* Aggregate data
	* Build a Kinesis Data Analytics application to read from the stream and aggregate metrics like unicorn health and distance traveled each minute.
* Process streaming data
	* Persist aggregate data from the application to a backend database stored in DynamoDB and run queries against those data.
* Store & query data
	* Use Kinesis Data Firehose to flush the raw sensor data to an S3 bucket for archival purposes. Using Athena, you'll run SQL queries against the raw data for ad-hoc analyses.

### Application Architecture
![](https://s3.amazonaws.com/hadoop357/RTArchitecture.PNG)
I will go through the project in the order of the architecture
* Before going through this architecture, I will create a Cloud9 instance and download the client.tar file first
* p.s You can use "Control + C" to stop it.

### Amazon Kinesis Data Streams
![](https://s3.amazonaws.com/hadoop357/KinesisStream.PNG)
* Put record for "./producer"
![](https://s3.amazonaws.com/hadoop357/putrecord.png)
* Use "./consumer" command to start reading sensor data from the stream.
* Use Amazon Cognito and manage Identity Pools. Identity pool ID will be used later.
* Use IAM (Identity and Access Management) and add inline policy that allow read and list.
* Use Identity pool ID in Contigo to do dataprocessing.
* Use command "./producer -name Bucephalus" to generate unicorns on map

### Aggregate Data
* The Amazon Kinesis Data Analytics application processes data from the source Amazon Kinesis stream that we created in the previous module and aggregates it on a per-minute basis. Each minute, the application will emit data including the total distance traveled in the last minute as well as the minimum and maximum readings from health and magic points for each unicorn in our fleet. These data points will be sent to a destination Amazon Kinesis stream for processing by other components in our system.

* Amazon Kinesis Data Streams -> Amazon Kinesis Data Analytics -> Amazon Kinesis Data Streams (aggregated)
* Analytics Application
![](https://s3.amazonaws.com/hadoop357/KinesisAnalyticsApp.png)
* Discover in Schema
	Build an Amazon Kinesis Data Analytics application which reads from the wildrydes stream built in the previous module and emits a JSON object with the following attributes each minute:
![](https://s3.amazonaws.com/hadoop357/SchemaDiscover.png)

* Run SQL to see rows of aggregated data.
![](https://s3.amazonaws.com/hadoop357/SQLRTanalytics.png)
#
	CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
	  "Name"                VARCHAR(16),
	  "StatusTime"          TIMESTAMP,
	  "Distance"            SMALLINT,
	  "MinMagicPoints"      SMALLINT,
	  "MaxMagicPoints"      SMALLINT,
	  "MinHealthPoints"     SMALLINT,
	  "MaxHealthPoints"     SMALLINT
	);

	CREATE OR REPLACE PUMP "STREAM_PUMP" AS
	  INSERT INTO "DESTINATION_SQL_STREAM"
	    SELECT STREAM "Name", "ROWTIME", SUM("Distance"), MIN("MagicPoints"),
			  MAX("MagicPoints"), MIN("HealthPoints"), MAX("HealthPoints")
	    FROM "SOURCE_SQL_STREAM_001"
	    GROUP BY FLOOR("SOURCE_SQL_STREAM_001"."ROWTIME" TO MINUTE), "Name";

Finally Connect to destination

* Read messages from the stream
	* commands: "./consumer -stream wild-summary 
	![](https://s3.amazonaws.com/hadoop357/readWildStream.png)

* Experiment
	* Exit previous unicorn
	* Run "./producer -name Bucephalus
	* Unicorn change 
	![](https://s3.amazonaws.com/hadoop357/UnicornChange.png)

### Process streaming data
* Amazon Kinesis Data Stream -> AWS Lambda -> Amazon S3 -> Amazon DynamoDB
* In previous section, the work data collecting have been finished.
* Amazon DynamoDB is a fully managed non-relational database service that provides fast and predictable performance with seamless scalability.
* Format:
	* Table name: UnicornSensorData
	* Partition key: Name
	* Sort key: StatusTime
* Create a WildRydesStreamProcessorRole with AWSLambdaKinesisExcutionRole and WildRyedsDynamoDBWritePolicy.
	* p.s Lambda run your code only when triggered, using only the compute resources needed.

* Create a Lambda function to process the stream
	* Create a Lambda function "WildRydesStreamProcessor" which will be triggered whenever a new record is available in the wildrydes stream.
![](https://s3.amazonaws.com/hadoop357/Lamdafunction1.PNG)
(I lost my file here again, I should edit .md file in local editor with auto saving function...)
* Monitor the Lambda function
	* use command "./producer -name Rocinante" again to start emiting sensor data to the stream with a unicorn name.
	* Use Monitoring AWS Lambda to get the data processing information.
![](https://s3.amazonaws.com/hadoop357/MONITOR1.PNG)
* Query the DynamoDB table
	* Explore DynamoDB table to get per-minute data point for each Unicorn.
![](https://s3.amazonaws.com/hadoop357/DynamoQuery.PNG)
	

### Store & qurey Data
* Application Architecture
	* Amazon Kinesis Data Streams -> Amazon Kinesis Data Firehose -> Amazon S3 -> Amazon Athena
![](https://s3.amazonaws.com/hadoop357/DeliveryStream.png)
* I use the previous mldata S3 bucket to store the collecting and analyzed data
* Create an Amazon Kinesis Data Firehose delivery stream
![](https://s3.amazonaws.com/hadoop357/ProcessRecord.png)
* Create an Amazon Athena table to query the raw data in place on Amazon S3 using a JSON SerDe.
	* Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL. 
	* Use the path of mldata to create a Athena table
	* Create table SQL statement:
#
	CREATE EXTERNAL TABLE IF NOT EXISTS wildrydes (
	       Name string,
	       StatusTime timestamp,
	       Latitude float,
	       Longitude float,
	       Distance float,
	       HealthPoints int,
	       MagicPoints int
	     )
	     ROW FORMAT SERDE 'org.apache.hive.hcatalog.data.JsonSerDe'
	     LOCATION 's3://YOUR_BUCKET_NAME_HERE/';
![](https://s3.amazonaws.com/hadoop357/AthenaQuery.PNG)
* The result in S3 bucket
![](https://s3.amazonaws.com/hadoop357/Logfileforwild.PNG)
### Explore and Query the batched data files
* Verify that Firehose is delivering batched data files to the bucket. Download one of the files and open it in a text editor to see the contents.
* Use "SELECT * FROM wildrydes" to get query data file

<a name="unicorn"></a> 
# Conclusion for the Unicorn project (30mins)
[Back to Menu](#menu)
(Notice: This is just a conclusion for the unicorn project. For more details or screeshots please jump to [episode 9](#app))
### Tool list
* Amazon S3
	* Used to store data
* AWS Lambda
	* Lambda run your code only when triggered, using only the compute resources needed.
* Amazon DynamoDB
	* Amazon DynamoDB is a fully managed non-relational database service that provides fast and predictable performance with seamless scalability.
	* In this project, I query the DynamoDB table to get per-minute data point for each Unicorn.
* AWS IAM
	* Use IAM (Identity and Access Management) and add inline policy such as WildRydesDynamoDBWritePolicy.
* Amazon Kinesis Data Analytics
	* The Amazon Kinesis Data Analytics application processes data from the source Amazon Kinesis stream that we created in the previous module and aggregates it on a per-minute basis.
	* Finally it will be aggregated again to the Amazon Kinesis Data Stream
* Amazon Kinesis Data Stream
	* All our works are dealing with the data stream and storing it.
	* Configure producers to put data records into a data stream. Configure consumers to continuously process data stream records.
	* It provides the function of Data Firehose and Data Analytics and we use Data Firehose to flush the raw sensor data to an S3 bucket for archival purposes. 
* Amazon Athena
	* Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL.
### Procedure
* Aggregate Data
	* Amazon Kinesis Data Streams -> Amazon Kinesis Data Analytics -> Amazon Kinesis Data Streams (aggregated)
	* Firstly I run ./producer or ./producer -name Bucephalus to generate Unicorns on map and use ./consumer to reading the sensor data. The Unicorns data is what we need to analye and aggregate.
	* Secondly I used Schema to build an Amazon Kinesis Data Analytics application which reads from the wildrydes stream built in the previous module and emits a JSON object with the following attributes each minute.
	* These analyzed data now need Lambda to collect at run time
* Process streaming data
	* Amazon Kinesis Data Stream -> AWS Lambda -> Amazon S3 -> Amazon DynamoDB
	* Firstly I used a Lambda function "WildRydesStreamProcessor" which will be triggered whenever a new record is available in the wildrydes stream to collect data from wildryes.
	* Then use DynamoDB table to get per-minute data point for each Unicorn and store.
	
* Store & query data
	* Amazon Kinesis Data Streams -> Amazon Kinesis Data Firehose -> Amazon S3 -> Amazon Athena 
	* There is another branch for data analyzing and store which using Firehose and SQL(Athena).
	* Use Kinesis Data Firehose to flush the raw sensor data to an S3 bucket for archival purposes. Using Athena, run SQL queries against the raw data for ad-hoc analyses.


# Docker and Containers

<a name="docker"></a>
# Why Docker?
[Back to Menu](#menu)
### What is docker?
* Docker is a computer program that performs operating-system-level virtualization and is used to run containers.
  * What is container?
    * A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.

### Docker is focused on the migration experience (20 mins)
* Docker is all about speed
![](https://s3.amazonaws.com/hadoop357/docker.PNG)
* Containers Reduce Complexity. Package all of the '?'.
* 80% MAINTENANCE, 20% INNOVATION, we use docker to reduce the maintenance and complexity drains budgets.
* e.g1 Paypal
   * 18 Month project (long)
   * Migrated 700+ apps (multi platform)
   * Now 150,000 containers (need management)
   * 50% dev productivity boost (benefit from docker)
* e.g2 MetLifte
   * 70% reduction in VM costs
   * 67% fewer CPU's (contrainer is light)
   * 10x average CPU utilization (management)
   * 66% cost reduction
 
****************************
### ! This is the old version of container beginner tutorial.
<a name="guide"></a>
# DevOps Docker Beginners Guide (1h)
[Back to Menu](#menu)

### Concepts
	* Docker engine
	* Containers & images
	* Image registries and Docker Store (AKA Docker Hub)
	* Container isolation
	
### docker container run hello-world
![](https://s3.amazonaws.com/hadoop357/hello_world.PNG)

### Docker Image
* Some Commands:
	* docker image pull alpine
		* The pull command fetches the alpine image from the Docker registry and saves it in our system. In this case the registry is Docker Store.
	* docker image ls
	* docker container run alpine ls -l
![](https://s3.amazonaws.com/hadoop357/Docker_detail.PNG)
	* docker container run -it alpine /bin/sh
		* Running a Linux shell inside the container.
	* docker container ls -a
		* list all containers that you ran.
![](https://s3.amazonaws.com/hadoop357/DockerInstance.PNG)

### Container Isolation

* Even though each docker container run command used the same alpine image, each execution was a separate, isolated container. Each container has a separate filesystem and runs in a different namespace; by default a container has no way of interacting with other containers, even those from the same image.

* In every day work, Docker users take advantage of this feature not only for security, but to test the effects of making application changes. Isolation allows users to quickly create separate, isolated test copies of an application or service and have them run side-by-side without interfering with one another. In fact, there is a whole lifecycle where users take their changes and move them up to production using this basic concept and the built-in capabilities of Docker Enteprise.
![](https://s3.amazonaws.com/hadoop357/DockerIsolation.PNG)
![](https://s3.amazonaws.com/hadoop357/DockerExec.PNG)

### Terminology
* Images - The file system and configuration of our application which are used to create containers. To find out more about a Docker image, run docker image inspect alpine. In the demo above, you used the docker image pull command to download the alpine image. When you executed the command docker container run hello-world, it also did a docker image pull behind the scenes to download the hello-world image.
* Containers - Running instances of Docker images — containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as an isolated process in user space on the host OS. You created a container using docker run which you did using the alpine image that you downloaded. A list of running containers can be seen using the docker container ls command.
* Docker daemon - The background service running on the host that manages building, running and distributing Docker containers.
* Docker client - The command line tool that allows the user to interact with the Docker daemon.
* Docker Store - Store is, among other things, a registry of Docker images. You can think of the registry as a directory of all available Docker images. You’ll be using this later in this tutorial.
