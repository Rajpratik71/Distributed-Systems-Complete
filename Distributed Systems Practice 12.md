# Distributed Systems Practice

Notes from learning about distributed systems in [GW CS 6421](https://gwdistsys18.github.io/) with [Prof. Wood](https://faculty.cs.gwu.edu/timwood/)

## **Table of Contents**

> All elapsed time contains time of material studying and time of note writing.
>
> Accoring to the description in course website, I only provide screenshot of "Bring it all together" in Docker topic. If more screenshots of other labs are requried, please contact me for them.

* [Docker and Containers](#Docker-and-Containers)
  * [Beginer Level](#Beginner-Level-mastery-of-Docker)
  * [Intermediate Level](#Intermediate-Level-mastery-of-Docker)
    * [Container](#Container)
    * [Networkding and Orchestration](#Networking-and-Orchestration)
  * [Bring it all together](#Bring-it-all-together)

* [SDN and NFV](#SDN-and-NFV)
  * [Beginer Level](#Beginner-Level-mastery-of-SDN)

## **Docker and Containers**

### **Beginner Level mastery of Docker**

1. ***Video: Why Docker?*** -- elapsed time: 20 mins
    * Docker is an opensource project released in 2013. It is a shift in frastructure that will affect whole IT related asepct.
    * Recap:
        * 90's: Mainframe to PC
        * 00's: Beramental to Virtual
        * 10's: Datacenter to Cloud
        * Now: Host to Container (Serverless)
    * Migration is hard. But Docker is focused on the migration experience because it was created for developer but system administers.
    * Fastest growing technology.
    * Docker make whole ecosystem of IT faster.
    * Applications runs in different enviorments. Docker allow users to get rid of different enviorments.
    * Docker frees maintains resources such that engineers have more time to innovate.

2. ***Lab: DevOps Docker Beginners Guide*** -- elapsed time: 40 mins

    * **Run first container**
        ```shell
        docker container run hello-world
        ```

        When a docker container command is typed, the docker engine would try to find the image with input name. If fails, it would look for a image from docker registry with the name. Then engine create the image.

    * **Docker images**
        * Just like Git workflow, user can *pull, push* a container image with command like:

            ```shell
            docker container pull [CONTAINER_NAME]
            ```

            ```shell
            docker container push [CONTAINER_NAME]
            ```

        * *Run command*
            * Using run command, will excute the container. If there are more commands follows by, them will be executed in the called container's terminal.
            * The default behavior of it is exit after excute followed command. We can change this by using flags such as [-it]

        * *List command*
            ``` shell
            docker container ls
            ```

            ls commmand will list all containers that are running. Using [-a] flag, a list of containers that we ran will be displayed. We can observe that different called containers was ran in different container instance, even they were called with same image.

    * **Docker Isolation**
        * If we create a file inside a container, there is no way for accessing it outside of the container. All container has its own namespace and context. This is container isolation.

### **Intermediate Level mastery of Docker**

#### **Container**

1. ***Video: What are Containers?*** --elapsed time: 25 mins

    * **Containers runtime definition**: a sandobx for a process--where a process has its namespace and Cgroups. Container lifecycle is coupled with container process.

    * **Image**: Images are binary data. Could be arranged in a image tree structure hierarchy such that 1) a image node can be shared, 2) descendant of a image will be affected by their parent image.

    * **Docker file** is used to tie container abstraction and image together. A docker file is an environment in a textfile starting with "FROM" indicating its parent image. There are any number of configurtion to create a specific docker image, such that can instaniate a container.

    * **Docker host**:
        * Using a *registry* which contains snapshot of images, a docker host can pull and push needed images.

        * With a *image cache*, a docker host does not need to handle whole image hierarchy but needed image node. There could be replication inside of the cache.

        * *Docker client* communicate with *demnon* inside of docker host though API, enabling container management actions like pull, create, run and commit.

        * Client can handle *network/storage configuration*. User can port forwarding and create/assign containers' network in network configration. In storage configuration, if user need to persist data beyond life circle of container, he can create a volume for storage.

2. ***Video: VMs Versus Containers*** --elapsed time: 15 mins

    |             | VM                                                                                                                                                                                                                                                   | Container                                                                                                                                                                                                                                       |
    |-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Description | Lays between physical hardware and operating system.                                                                                                                                                                                                 | Lays between application layer and operating system                                                                                                                                                                                             |
    | **Application** layer| Needs lots of dependencies to run                                                                                                                                                                                                                    | Dependencies from OS could be packaged up as a docker file                                                                                                                                                                                      |
    | **OS** layer          | Has virtual hardware layer.  Permutation is small in VM, total number of drivers is small.  Require some configuration of dependency for loading and automation. | A docker engine embedded in the operating system. |
    | *VM* layer         | Has limited number of network interfaces(NICs), storage interfaces.  Can scale at demand.                                                                                                                                                      | (Optional)Can minimize amount of dirver and technologies.                                                                                                                                                           |
    | *Hypervisor* layer | Interact with physical hardware, like hardware NIC, hardware storage.  Have agents that monitor RAID card and hardwares in hypervisor.  Have kernel modules.                                                     | (Optional) Can mask all hardware interface for VM, and container host.                                                                                                                                                                |
    | **Physical hardware**   | \                                                                                                                                                                                                                                                    | \                                                                                                                                                                                                                                               |

3. ***Lab: Docker Intro*** --elapsed time: 35 mins

    * **Run docker containers**

        User can run containers that have different application running, including single task container, interactive container and background container. Using coressponding flag in run command can make it.

        * *Default*:

            ``` shell
            docker container run [FLAG] [CONTAINER_NAME] [TASK]
            ```

        * *Interactive* flag:

            ``` shell
            docker container run --interactive [FLAG] [CONTAINER_NAME] [TASK]
            ```

        * *Background* flag:

            ``` shell
            docker container run --detach [FLAG] [CONTAINER_NAME] [TASK]
            ```

    * **Package and run a custom app**

        With a dockerfile, user can package customized application as a docker image.

        * *Dockerfile syntax*
            * *FROM* specifies the base image to use as the starting point for this new image you’re creating. For this example we’re starting from nginx:latest.

            * *COPY* copies files from the Docker host into the image, at a known location. In this example, COPY is used to copy two files into the image: index.html. and a graphic that will be used on our webpage.

            * *EXPOSE* documents which ports the application uses.

            * *CMD* specifies what command to run when a container is started from the image. Notice that we can specify the command, as well as run-time arguments.

        * *Build with dockerfile*
            ```shell
            docker image build [FLAG] [...]
            ```
            Use the command, user would create a new docker image using the instructions in the dockerfile.

    * **Modify a running application**
        * *Bind mount*

            If we are modifing source code on a running system, it would be nightmare to stop the container, rebuild the image and test new version everytime.

            ``` shell
            docker container run \
            --detach \
            --publish 80:80 \
            --name linux_tweet_app \
            --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \
            $DOCKERID/linux_tweet_app:1.0
            ```

            In the above example, user can use bind mount to start an application with [--mount] flag. It enables mounting source code directory into a running container. The container will react to every change immediately.

        * *Modify source and test*

            Let us make change to the source file naming it as [linux_tweet_app:2.0].

            ``` shell
            docker container run \
            --detach \
            --publish 80:80 \
            --name linux_tweet_app \
            $DOCKERID/linux_tweet_app:2.0
            ```

            User can test new version by changing file name.

        * *Store image in Docker Hub*

            Docker provides a system called Docker Hub for user to manage their images with version control. It is pretty like Github system workflow.

             ``` shell
             docker image push [$DOCKERID]/[FILE_NAME]
             ```

             With push command after login, user can push the assigned image to Docker Hub.

4. ***Lab: Doing more with Docker Images*** --elapsed time: 40 mins

    * **Image creation from a container**

        User can create a image from a existing stopped container with commit command
        ```shell
        docker container commit [CONTAINER_ID]
        ```

    * **Image creation using a Dockerfile**

        User can create a image using a Dockerfile. It has serval configuration, including FROM, RUN COPY WORKDIR, and CMD. After writing Dockerfile, user can create a image directly using run command
        ```shell
        docker container run [IMAGE_NAME]
        ```

    * **Image layers**

        After user modified the application running in the container, he can rebuild a new image to test modification. The interesting part during the build procedure is it says "Using cache".
        Because docker image has hierarchy, some of replicated image are stored in Docker cache. Docker will recognize difference between new created image between existing ones so that it will use cached version instead of going through all procedure again.

    * **Image Inspection**
        ```shell
        docker image inspect [IMAGE_NAME]
        ```
        The command allows user to inspect hierarchy tree of a docker image.

5. ***Video: VMs Versus Containers Deep Dive*** --elapsed time: 15 mins

    |                     | VM                                                                                                                                                                                                                                                                                                                        | Container                                                                                                                                                                                                                                                                                                                                                                       |
    |---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Size**                | 1. All in a disk image, including kernel+Init system, userspace programs, and applications.   2. Size is based on the size of user space and application. 3. Range from hundreds of megabytes to tens of gigabytes.                                                                                                       | 1. Size mainly depends on applications in the container. Range from tens of megabytes to gigabytes  2.Smallest: applications can be well containerized--all data are static binary without OS dependency(e.g Go language programs)  3. Largest: applications need userspace tool, a base container could be used to contain application containers  (e.g Ubuntu base container) |
    | **Isolations/Security** | 1. Boundary exists. VM is like a sandbox.  2. Attackers need to break through from application to OS so that can break the fence.                                                                                                                                                                                         | 1. Not insecure but secure on the kernel that it is running on.  2. Attackers need to break kernel bugs. It is possible but rare.                                                                                                                                                                                                                                               |
    | **Boot time**           | Serval start-up time would be added up, including two sections:  1. System check section(3-4secs): x86 post + EFI/BIOS boot/check + kernel boot + Init startup  2. Process startup section(500ms) 3. Improvement: a. Use EFI instead of BIOS b. Use modern Init system. These can bring startup time closer to container. | 1. Set up sandbox: no time, because it is kernel operation.  2. Run process(500ms)                                                                                                                                                                                                                                                                                              |                                                                                                                   |

#### **Networking and Orchestration**

1. ***Lab: Docker Networking*** --elapsed time: 45 mins

    * **Bridge Networking**

        * *Bridge* is default network.

            We can connect to Internet from a container using the bridge network which is associated with Linux bridge driver. The default behavior is assigning all new containers to the bridge network. So we can connect a new container directly once it is created.

        * User can *map contain's port to container host's port*. This could be done by using [-p] flag when create a container.
            ```shell
            docker run --name [CONTAINER_NAME] -d -p [HOST_PORT]:[CONTIANER_PORT] nginx
            ```

    * **Overlay Networking**

        * *Swarm* could be used for orchestration.

            ```shell
            docker swarm init --advertise-addr $(hostname -i)
            ```
            With the above command, we can create a swarm manager. Then we can add a worker from another container with the token.
            ```shell
            docker swarm join --token [TOKEN] [MANAGER_IP]
            ```

        * *Overlay Network*:

            Users can define their own network based on the swarm network, which is called overlay network. Overlay network could be created by the command:
            ```shell
            docker network create -d overlay [OVERLAYNET_NAME]
            ```
            Then we can create service that uses the network.
            ```shell
            docker service create --name [SERVICE_NAME] --network [OVERLAYNET_NAME] --replicas [REPLICATION_NUMBER] [APPLICATION_NAME]
            ```

2. ***Lab: Swarm Mode Introduction*** --elapsed time: 20 mins

    * **Swarm Architecture**

        After initilated Docker Swarm Mode, we can view its structure via following command:
        ```shell
        docker node ls
        ```
        There would be managers and workers. The number of *managers* woule usually be 3,5 or 7 because quorum status requirement. They can also be worker. *Wokers* would execute assigned task but cannot be manager in same time.  
    * **Deploy a stack**
        * A *Stack* is a group of services that are deployed together. User can use a plaintext file to define a stack, which usually named as a .yml file. We can delopy a stack in a manager's terminal
            ```shell
            docker stack deploy --compose-file=[.ymlFILE_NAME] [STACK_NAME]
            ```
        * CheckWe check *info of a stack* by running
            ```shell
            docker stack ls # simply list number of services in the stack

            docker stack services [STACK_NAME] # detail of each services in the stack

            docker service ps [SERVICE_NAME] # detail of each tasks in the service
            ```
        * *Stack, Service, and Tasks*

            We can observe that there are services and tasks in the structure of Swarm stack. *Service* are a stack component, including a container image, number of replicas, ports and update policy. *Tasks* are atomic unit of a service and scheduling in Docker. One container instance per task.

    * **Scaling an application**

        Two methods of scaling--automation via Docker's API, and manual via edit .yml file or typing terminal command:
        ```shell
        docker service scale [STACK_NAME]=[SCALING_NUMBER]
        ```

3. ***Video: Kubernetes vs Swarm*** --elapsed time: 5 mins

    |             | Swarm                                                    | Kubernetes                                            |
    |-------------|----------------------------------------------------------|-------------------------------------------------------|
    | Description | A built-in orchestration system in docker             |  A popular orchestration system for docker by Google  |
    | Features    | Run a docker in a cluster Redoplyments with no down-time | Much more features                                    |
    | Popularity  | Simpler than Kubernetes Good for beginner to learn       | Most of real world project                            |

4. ***Video: Kubernetes in 5 Minutes*** --elapsed time: 15 mins
    * **Architectural component**:

        * *Kubernetes cluster services*

            User feed the cluster services a specific configuration. And cluster services will run the configuration in infrastructure. This component has a set of API that takes charge of communication.

        * *Woker* is a container. It has kuberlet process that runs for communicating with kubernetes services.

    * **Use case**:

        * *Feed* desired state file(.yaml file) to kubernetes cluster services. It is a deployment file containing two fundanmental parts:1)Pod. There could be a running container. Thus, it needs to specify container image(s) and 2)Replica specify the number of needed image.

        * Kubernetes cluster services will try to *schedule* feeding Pod and checking. If one of a worker *die*, the kubernetes cluster services will reschedule new place for instantiating lost Pod and Replica.

5. ***Install Docker on a cluster of EC2 VMs and use Kubernetes to orchestrate them*** --elapsed time: 5 hours
    I provide screenshot for this lab because I thought it is the final lab for Docker topic at first. Anyway, screenshot helps demonstrating steps in the lab.

    * **Steps**
        1. *Docker*
            I installed docker pretty easily.

            ![Docker](./screenshots/docker-lab/docker.png)

        2. *Kubernetes*
            * Python, pip, awscli, and kubectl

                Because the tool--Kops--I was using to setup Kubernete require AWS Auth. I need to install awscli first. Nothing complex here.

                ![python_pip_awslic_kubectl](./screenshots/docker-lab/python_pip_awslic_kubectl.png)

            * Set up Kops, Aws user permission, and bucket
                Kops is a tool to manage Kubenetes cluster, especially for AWS users. It requires AWS access key for later actions. I set up a new user who has serval permissions to create a Kubenetes cluster.

            * Orchestrate cluster
                I create a cluster that has 2 nodes with AWS SSHpublickey, Then I edit, update, and validate a new Kubernetes cluster using kops.
                ```shell
                kops create cluster --node-count=2 --node-size=t2.medium --zones=us-east-1a --name=${KOPS_CLUSTER_NAME} # Create cluster of two nodes
                kops edit cluster --name ${KOPS_CLUSTER_NAME} # Edit configuration file of created cluster
                kops update cluster --name ${KOPS_CLUSTER_NAME} --yes # Update cluster to docker
                kops validate cluster # Verify created cluster ready or not
                ```

                ![Validating cluster](./screenshots/docker-lab/validate.png)
                ![Ec2 cluster](./screenshots/docker-lab/cluster_creation.png)

                I followed Kubernetes documentation to set up Kubernetes dashboard. And access it via RESTful API using cluster master domain. My apology of showing Chinese UI here because I cannot change it.

                ![Kubernetes Dashboard](./screenshots/docker-lab/dashboard.png)

                Then I deploied an Nginx image to my cluster. Then expose its services.

                ![Deploy Nginx](./screenshots/docker-lab/deploy.png)
                ![Expose its services](./screenshots/docker-lab/service_expose.png)

                At last, I scale it up.

                ![Scaling out](./screenshots/docker-lab/scaling.png)

    * **Difficulties**

        1. I lanuched a instance of EC2. I installed docker in it easily. However, the installion of Kubernetes became a nightmare in AWS. All tool I found require AWS access key for install k8s which is a banned feature in AWS educate account. I tried to fix this out turing impossible and wasted more than 2 hours.

        2. Later, I set whole enviorment up using my personal AWS account. Left Docker alone, I installed Kubernetes using Kops. Another annoying thing is that when I tried to create SSHpublic key using AWS crendential, the default file name of the is somethingelse but regular 'id_rsa'. I scanned whole documation for a hour to fix my public key. AWS is quite annoying about auth stuff.

### **Bring it all together** --elapsed time: 4 hours

In the AWS Tutorial: Break a Monolith Application into Microservices, I broke a application into microservices and deploy them into containers. It provides a monolith application to play with.

There are four sections included--1) Containerize the application, 2) deploy the application, 3) break the monolith, and 4) deploy microservices.

1. **Containerize the monolith**

    * Install dependencies and get application source

        We need to install awscli and docker where we work. There is not much to talk about installation and git clone --depth 1 provided project.

        ![Install wheel](./screenshots/docker-fianl/installation.png)

    * Build and push Docker Image

        After login AWS, we can create a container registry repo for storing images.

        ![ECS repo](./screenshots/docker-fianl/ECSrepo.png)

        Then we need to build the code, tag, and push the image to it.
        ```shell
        docker build -t api .                                                       # Build
        docker tag api:latest [account-id].dkr.ecr.[region].amazonaws.com/api:v1    # Tag
        docker push [account-id].dkr.ecr.[region].amazonaws.com/api:latest          # Push
        ```
        ![Pushed image](./screenshots/docker-fianl/pushed_image.png)

2. **Deploy the monolith**
    * Create ECS cluster, and write task definition
        We need to create a cluster for deployment.

        ![Cluster](./screenshots/docker-fianl/cluser.png)

        Then add task definition to the cluster

        ![Task Definition](./screenshots/docker-fianl/task_defi.png)

    * Add and configure Application Load Balancer
        We need to add a application load balancer. And then configure its target group and listener.

        ![Application Load Balancer](./screenshots/docker-fianl/app_load_balancer.png)
        ![Configure target group](./screenshots/docker-fianl/target_group.png)
        ![Configure listener](./screenshots/docker-fianl/lisner.png)

    * Deploy the Monolith as a service in container
        Create service in load balancer.

        ![Create service](./screenshots/docker-fianl/service.png)
        ![Test deployment](./screenshots/docker-fianl/testing.png)

3. **Break it into microservices**

    The application is already broken into microservices in sample code. After creat image repo for each of microsevices, we can directly build it, then tag and push each images.

    ![Create service](./screenshots/docker-fianl/microservice_image.png)
    ![Create service](./screenshots/docker-fianl/microservice_repo.png)

4. **Depoy microservices**

    Here we are at the final step. It is pretty similiar as pervious procedure but we need to set up microservice seperately.
    * Task definition

        ![Write task definition](./screenshots/docker-fianl/microservice_task_definition.png)

    * Configure Application Load Balancer

        ![Configure target group](./screenshots/docker-fianl/microsevice_target.png)

    * Configure listener rules

        ![Configure listener rule](./screenshots/docker-fianl/microservice_listener.png)

    * Deployment

        ![Deployment](./screenshots/docker-fianl/microservice_services.png)

        At last, we can test spilited microservices. For example, we can test <http://[DNS name]/api/threads>

        ![Testing](./screenshots/docker-fianl/microservice_result.png)

Actually, this tutorial is easier for me after the annoying procedure I walked through on above lab.  But it is still time consuming because my PC is running Windows10home without Hyper-V, which means I cannot install docker locally. I tried serval alternative solutions for this. At last, I use another personal cloud server completing the lab.

## **SDN and NFV**

### **Beginner Level mastery of SDN**

1. ***Video: Introduction to SDN*** --elapsed time: 30 mins
    * **Primary Goal**
        The main goal of SDN is for the network to be open and programmable. If a specific network behavior is required, users should be able to develop and install application for the purpose.

    * **3 layer model of OS and SDN**

        |        |                                        OS model                                        |                                                                             SDN model                                                                             |
        |:------:|:--------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------:|
        |  North |                       Application--Customize OS for users' demand                      |                                                   Network Application--Satisfy user's needs but network related                                                   |
        | Center |    OS--Manage access from applications to underlying hardware, using core services.    |                                  Network OS(SDN controller)--Interfaces with network nodes and provides programmable interfaces.                                  |
        |  South | Hardware--Handled by OS. Typical components are CPU, storage, memory and network card. | Network forwarding device--Receive and handle packets, update counters. Actions includes dropping packets, modifying packet headers and sending packet via ports. |

    * **Packet Flow**
        * *On arrival*: a packet arrives forwarding device, it would parse header then either already knows what to do or queries the network controller.

        * *In middle*: network operating system handle queries from south, letting netapplications that running on it determine action to the packet.

        * *At last*: Network operating system translate order down to forwarding device. They would cache instructions as well.

    * **Layer details**
        * *Forwarding device*

            It could be hardware switch that supports programmable interface with higher performance, or software switch with better flexibility.

        * *Southbound Interface*

            There are many types of information that needs to be communicated. Typically, we use 1) OpenFlow as protocol to handle packet, 2) OVSDB protocol to manage network device configuration and so on.

        * *Network operating system*

            Runs with core services, like topology, inventory, statistics service, and host tracking.

        * *Application Interface*

            SDN controller provides simplified abstraction of infrastructure(a single large switch) for applications. It could use Java API. Northbound interface is RESTful interface. This area is lacking standardization.

    * **Avaiability and scalability**
        An SDN controller is logically cntralized ranther than physically centralized. Such that it may avoid network failure result by single controller failure, and avoid scaling limation.

        * *Cluster/Teaming*
            Use a cluster of system to balance workload.

        * *Seperation based on regions*
            Use regional SDN controller to handle traffic in the region. Controllers communicate via East/West Protocol.

    * **SDN vs traditional networks**

        |      | Traditional Network                                                                           | SDN                                                                                       |
        |------|-----------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
        | Pros | \                                                                                             | 1. No needs to handle distributed protocol 2. No needs to configure policies in all nodes |
        | Cons | 1. Proprietary locked box--control is coupled to data 2. Each node is configured individually | SDN controller is very complex                                                            |

2. ***Tutorial: Using the OpenDaylight SDN Controller with the Mininet Network Emulator*** --elapsed time: 2 hours

    This tutorial guides me to simulate an SDN controller (OpenDaylight) with a SDN network (Mininet) using OpenFlow. All working enviorment are sitting on VM running in VirtualBox. There are three sections included here--1) set up Mininet VM, 2) set up OpenDaylight VM, and 3) capture OpenFlow messages

    * **OpenDayligh VM**
        * *Start OpenDaylight VM. Connect it*

            Using the Ubuntu Server ISO, we can installed a new Vm in Virtual Box. After set up a loopback network adapter, we can connect to the VM using putty based on the IP gained from ```ifconfig``` command

        * *Set up dependencies*

          * OpenDaylight is Java program and packaged in karaf container. Thus we need install Java for it.
          * Then we need to download and tar the OpenDaylight file. We can start it with ```karaf``` inside of the container.
          * Install features for testing and GUI.

    * **Mininet VM**
        * *Start and connect Mininet VM*

            There is a VM .ovf file provided by Mininet project. We can import the MininetVM to virtual box with it. Similar, we can connect to it using putty.

        * *Start Mininet*

            Inside of the MininetVM, there are many pre-installed dependcries for supporting Mininet. We can start it directly withou any installation. We can test the network by ping something, which is worked out.

    * **Caputure OpenFlow message**
        * *Access OpenDaylight GUI*

            Once OpenDaylightVM is set up, we can access GUI from browser. There are lots of SDN features that we should know. Topology, nodes and Yang which provides SDN switches.

        * *Capture messages*

            Keep two VM and our SDN application on them running. Now we had set up a SDN, which could be validated if we can capture OpenFlow message or not. Connecting to MininetVM, we can start Wireshark to detecting packet. After creating a filter for OpenFlow message, we can observe message flowing on.