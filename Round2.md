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
