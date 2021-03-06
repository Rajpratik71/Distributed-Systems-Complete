# Distributed Systems Practice
Notes from learning about distributed systems in [GW CS 6421](https://gwdistsys18.github.io/) with [Prof. Wood](https://faculty.cs.gwu.edu/timwood/)

## Area 1 - Docker and Containers

* [Why Docker Video ~10 mins](https://www.youtube.com/watch?v=RYDHUTHLf8U&t=0s&list=PLBmVKD7o3L8tQzt8QPCINK9wXmKecTHlM&index=23)
  * Docker and containers are leading to a major infrastructure shift
  * @gordonTheTurtle
  * Docker seeks to provide speed in Development, Build, Test, Deploy, Update, and Recovery
  * Tons of big companies are shifting to Docker (two big case studies: PayPal and MetLife)
  * Big boost in developer productivity and decrease in infrastructure costs

* [Docker Beginner Lab ~50 mins](https://training.play-with-docker.com/ops-s1-hello/)
  * Understanding how the docker repository works to host images
    * Docker pulls an image from the docker hub
  * Docker creates a container from the image, which runs the executable
  * Docker run (example command: `docker run alpine ls -l`)
    * Pulls container if necessary (container named `alpine`)
    * Starts container (container named `alpine`)
    * Runs executable (executable that is run is `ls -l`)
  * To use an interactive terminal, use the `-it` argument
  * `docker container ls` shows all running containers
  * `docker container ls -a` shows all containers including statuses
  * Docker containers are isolated from each other
  * You can start containers and pass commands to them to `exec`

* [What are Containers Video](https://www.youtube.com/watch?v=EnJ7qX9fkcU)
  * At its simplest definition a container is an isolated process within a host OS
  * Container processes and its container lifecycle are tightly coupled
  * Containers are created from images - image layering (covered in "Doing More with Docker Images Lab")
  * Dockerfiles create images
  * Docker registry and cache
  * Docker client does container lifecycle management as well as network and storage configuration

* [VMs Versus Containers Video](https://www.youtube.com/watch?v=L1ie8negCjc)
  * VM needs hypervisor, which does NIC, storage, hardware monitoring (agents, kernel modules)
  * VMs have NIC, storage, can be sized
  * Containers have docker engine in OS
  * Container image contains OS dependencies and application

* [Docker Intro Lab ~20 mins](https://training.play-with-docker.com/beginner-linux/)
  * Introduction was overlapping content from Docker Beginner Lab
  * `cat /etc/issue` is a good way to tell what you are running on
  * Committing containers is bad, better to use a repeatable `Dockerfile`
  * Learned how to start up mysql in the background on a container
  * Learned how Dockerfiles work and the basic syntax
  * `stop` gracefully stops container, `rm` permanently removes it, `--force` removes forcefully without shutting down
  * Learned how to build a new image and push to Docker Hub when you want to update

* [Doing More with Docker Images Lab ~25 mins](https://training.play-with-docker.com/ops-s1-images/)
  * If we install an image then make some changes to it on the interactive shell, we can see the differences using `docker container diff <containerID>`
  * If we wanted a new image that contained these changes we could use `docker container commit <containerID>`
  * This is useful for creating a new image, however a Dockerfile is even more useful
  * Add tags to images using `docker image tag <imageID> <tag>`
  * Dockerfile specifies how to setup a container based off of a base image
  * Docker image history keeps track of image layers (similar to git commit history)
  * Layers saves us time by caching levels of builds to avoid having to go through the whole process when unnecessary

* [VMs Versus Containers Deep Dive Video](https://www.youtube.com/watch?v=PoiXuVnSxfE)
  * Container images can be very small in size if built using container-oriented technologies
  * Otherwise they can be similar in size to VMs
  * VM isolation is as far as we know impenetrable (fully isolated x86 instances)
  * Containers are only as secure as the process they are running on - kernel insecurities -> container insecurities
  * Containers start fast but we can speed up VMs by using EFI and systemd to do things in parallel and faster

* [Docker Networking Lab ~35 mins](https://training.play-with-docker.com/docker-networking-hol/)
  * `docker network` is the primary command to manage networks with Docker
  * Docker uses a docker0 network to bridge containers to the host network
  * Able to map ports into the container and use `docker ps` to view port mappings
  * Swarms are replica groups of containers led by leaders with workers
  * Created a swarm with one node as a manager and joined the second node as a worker
  * Overlay networks are created on the manager node and deployed to worker nodes as needed, which is cool
  * You can create a service on the overlay network with replicas, which will deploy onto nodes and wait for it to start (ex. `docker service create --name myservice \
--network overnet \
--replicas 2 \
ubuntu sleep infinity`)
  * Overlay network gives two nodes IPs on a network
  * Service discovery works by running a local Docker nameserver on 127.0.0.11:53 - this allows us to use service names rather than IP addresses if desired
  * Leave a swarm with `docker swarm leave --force`

* [Swarm Mode Introduction Lab ~30 mins](https://training.play-with-docker.com/ops-s1-swarm-intro/)
  * Setup swarm in identical manner to last tutorial - `docker swarm init --advertise-addr $(hostname -i)`.  Join swarm from other node with the command in the output of the first node's `docker swarm init` command
  * `docker node ls` lists nodes in swarm (only works on manager)
  * `docker-stack.yml` can be used to deploy a "stack" (list of services that can be deployed together)
  * `docker stack deploy --compose-file=<stack file> <stack name>` is the command for deploying a stack based on a config
  * `docker stack` has other useful sub-commands such as `services`, `ps`, and `ls`
  * Deployed voting application via stack and saw replicas running on multiple nodes
  * `docker service scale <servicename>=<scalesize>` is an insanely awesomely simply way to scale your application

* [Kubernetes vs Swarm Video](https://www.youtube.com/watch?v=L8xuFG49Fac)
  * Both are orchestration systems for Docker
  * Kubernetes and Swarm are in competition - have to choose between the two
  * Kubernetes is probably the more popular of the two
  * Kubernetes is more mature, with more features, and is more widely used

* [Kubernetes in 5 Minutes Video](https://www.youtube.com/watch?v=PH-2FfFD2PU)
  * Kubernetes enforces desired state management
  * Cluster services and workers
  * Workers have kubelet processes which communicate with cluster services
  * specify desired application state with a `.yml` file
  * Pod configuration specifies container images, and service info
  * Specify number of replicas for each pod type
  * Cluster services is in charge of scheduling and allocating resources to meet the desired state
  * When things go wrong cluster services is in charge of rearranging to reattain desired state

* [Learn more about Kubernetes individually ~20 mins](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
  * Followed the linked tutorial
  * Used minikube (a small-scale local Kubernetes deployment) to do the following:
    1. Create a cluster
    2. Deploy an application
    3. Expose the app
    4. Scale the number of instances of the application within the minikube cluster
    5. Update and roll back the application across all scaled instances within the cluster - rolling back the application was incredibly easy with a single command specifying version

* [Install Docker on cluster of EC2 VMs and use Kubernetes to Orchestrate Them ~75 mins](https://kubernetes.io/docs/setup/turnkey/aws/#getting-started-with-your-cluster)
  * Followed the linked tutorial
  * Created 3 EC2 instances and managed them with Kubernetes
  * Followed [this guide](https://kubernetes.io/docs/getting-started-guides/ubuntu/) to setup the Kubernetes cluster on Ubuntu EC2 instances
  * Used [this guide](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/) to install a simple nginx application
  * Updated the application
  * Then used Kubernetes to roll it back to the previous version across the cluster

* [AWS Tutorial: Break a Monolith Application into Microservices ~100 mins](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/?trk=gs_card)
  1. Containerize the Monolith
    * The first step was to get set up (install docker, setup the AWS CLI, clone the repo)
    * Then I set up a container on ECR following the direction
    * The final step of containerizing the monolith was building and pushing the docker image to ECR - this was fairly straight forward as they set it up for us in the project
    * This process for creating the image looks like the following: ![Container Image Build Process](container-image-build-process.png)
  2. Deploy the Monolith
    * Using classic web app tools - load balancer with target groups
    * Setup containers as the target group using ECS
    * Used CFTs to setup a resource stack and used this to create / update the template of what we want
    * Cloud Formation is a great tool from AWS I became familiar with this summer at Capital One
    * setup the monolith service and tested the load balancer / request rotation
    * service name points at load balancer
    * added a field to the user data model so I could get a better understanding for how the application worked: ![JSON With Added Field](json-with-added-field.png)
  3. Break the Monolith
    * Breaking the monolith into multiple microservices is beneficial because of isolation of crashes (only one part will break at a time), isolation for security (breach of one service hopefully won't impact others), independent scaling (no need to scale whole app if only one part needs scaled), and development velocity
    * Instead of one ECR repository for the monolith, we create one per microservice
    * Build one image per service
    * Break the DB up as well for each service
  4. Deploy Microservices
    * Configure target groups for each endpoint `/api/users` -> users microservice target group etc
    * Setup the 3 autoscaling groups instead of just 1
    * Transition traffic over
    * Kill the running monolith application
    * Again using CFT for config    
    * Check to make sure that requests are distributed among the microservices among the replicas
    * Diagram for this process: ![microservice-deployment-diagram](microservice-deployment-diagram.png)
  5. Clean up
    * Deleted everything

## Area 2 - Cloud Web Apps

* [AWS Launch a VM Tutorial ~10 mins](https://aws.amazon.com/getting-started/tutorials/launch-a-virtual-machine/)
  * Created a VM on EC2 - have done this before so went pretty quickly
  * Go to EC2 console and click Launch Instance
  * Specify things about the instance like OS type, number of CPUs, RAM, Disk Space, networking config
  * Ensure SSH key is present so you can login from your machine
  * `ssh` in and run some commands / play around
  * Expose some ports like 80 or 8080 and run a simple webserver.  Access it at the public IP
  * Disconnect and terminate the instance

* [Intro to S3 QwikLab ~20 mins](https://awseducate.qwiklabs.com/focuses/30?parent=catalog)
  * Creating a bucket is quite simple using the web UI
  * Also very easy to upload via the web UI
  * Objects in S3 have their own URLs
  * Can set default to be public or private
  * If private, need to explicitly allow access to certain objects
  * Bucket policies let you apply rules across buckets without having to specify rules for every single upload
  * Versioning lets you keep track of file versions in the bucket
