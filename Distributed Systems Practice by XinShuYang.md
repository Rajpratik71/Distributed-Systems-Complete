# Distributed Systems Practice
Notes from learning about distributed systems in [GW CS 6421](https://gwdistsys18.github.io/) with [Prof. Wood](https://faculty.cs.gwu.edu/timwood/)

## Area 1 Docker and Containers  
### Beginner Level:  
#### [Video: Why Docker?(16min)](https://www.youtube.com/watch?v=RYDHUTHLf8U&t=0s&list=PLBmVKD7o3L8tQzt8QPCINK9wXmKecTHlM&index=23)
**Theory Definition:**  
Container in linux os is many container processes themself. 
In the container process, it has its own process name space and cgroups.  
So,these process can be run in sandbox to be isolated.  
**Dockerfile:** use "from" at first,"from"is the parent image but this docker file is inheriting from.(use a tree structure to define the dependent library relationship)  
Dockerfile ultimately ends up creating an image.It creats the image tree that we can use to instantiate containers.  
dockerfile is the starting point for an image.  
You can build you image both from **dockerfile** and **current container**.
Then you can use the image to build more container.  
When you run a container,it will pull the file from the registory.
Then to the cache,and then set up the c group in the process name space.  
#### [Lab: DevOps Docker Beginners Guide(35min) ](https://training.play-with-docker.com/ops-s1-hello/)
*Docker basic operator*  
__docker image ls__ `[search local image file]`  
__docker container ls__ `[search running container]`  
__docker container ls -a__ `[search historical container]`  
__docker image pull &lt;name&gt;__ `[download image files]`  
__docker container run &lt;containername&gt; &lt;operator&gt;__
`[run container,return rusult ,close container]`  
__docker run -it &lt;container name&gt; &lt;operator&gt;__
`[run container,get into container,return result,still run container(exit:quit)]`  

echo "hello world" &gt; hello.txt
`[build hello.txt in current path,write"hello world"](This file in container will remain)`

__docker container start &lt;container ID&gt;__  
__docker container exec &lt;containerID&gt; &lt;operator&gt;__

  
### Intermediate Level  

#### [Lab: Docker Intro(35min)](https://training.play-with-docker.com/beginner-linux/)  
3 Basic docker operator type:  
**To run a single task:** This could be a shell script or a custom app.  
**Interactively:** This connects you to the container similar to the way you SSH into a remote server.  
**In the background:** For long-running services like websites and databases.
#### [Lab: Doing more with Docker Images(40min)](https://training.play-with-docker.com/ops-s1-images/)
*(1)From container  docker image build*  
```bash
docker container run -ti ubuntu bash [run container and get into container and keep it running]
{do waht you want}
exit  [exit and stop the container]
docker container ls -a [get the container ID]
docker container commit <CONTAINER_ID>  [create the image based on container]
docker image ls
docker image tag <IMAGE_ID> <NEW NAME>  [ give the image name]
docker container run <IMAGE NAME> <OPERATOR that you can run in your container> [use the image to run operator directly]
```
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/9Dockerimage.png)
*(2)From Dockerfiles Use the dockerfile to build the image*  
operator:  
```bash
vi index.js    [create a file named index.js and use vim enter the edit page]
input "i" to go to the insert model
var os = require("os");
var hostname = os.hostname();
console.log("hello from " + hostname);
:wq      [write and quit]
vi Dockerfile       [create the docker file]
FROM alpine
RUN apk update && apk add nodejs
COPY . /app
WORKDIR /app
CMD ["node","index.js"]
:wq
docker image build -t hello:v0.1 .     [notice the last "."]
```
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/10Dockerfile.png)  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/11Layers.png)  

#### [Video: What are Containers?(19min)](https://www.youtube.com/watch?v=EnJ7qX9fkcU)  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/1Container.png)
&lt;Video1-The basic structure of container&gt;
#### [Video: VMs Versus Containers(15min)](https://www.youtube.com/watch?v=L1ie8negCjc)  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/2VM&Container.png)
&lt;Video2-The comparison between VM and containers&gt;
#### [Video: VMs Versus Containers Deep Dive(13min) ](https://www.youtube.com/watch?v=PoiXuVnSxfEc)  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/3VM&Container_advanced.png)  
&lt;Video3-The advanced details between VM and containers&gt;  
*(1)* **The container size:** It is depend on the programming language.For example, the application used by go language can be 1 M size.However,some traditional software will need GB level storage. And if the application need user space tool. The container will build based on another user space container.  
*(2)* **Isolation:** VM is a sandbox, it has a clear boundary,so the attack wil be very hard because the vm is very isolated on its host machine.In container side, it is easier to attack the kernel from the process in container.  
*(3)* **Boot time:** Boot time in VM has 2 sections. One is System check section(including X86post,EFI or Boot check,kernel boot,init startup),usually3-4seconds;The second is porcess running section(about 5ms). If you use system v or parallel on VM. Its speed will increase close to container. Then container is started.First setting up the process sandbox and the other one is  staring up the application. This is very fast.
#### [Video: Kubernetes vs Swarm(10min)](https://www.youtube.com/watch?v=L8xuFG49Fac) 
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/4docker_swarm.png)  
&lt;Video4-Docker Swarm basic structure&gt; 
#### [Video: Kubernetes in 5 Minutes(10min)](https://www.youtube.com/watch?v=PH-2FfFD2PU) 
docker swarm is less powerful than the kubernate. But it is easier to use.
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/5kubernetes.png)  
&lt;Video5-Kubernetes simple structure&gt; 
There are one **kubernate cluster server** and several **worker nodes**.Each worker has a kubernate process which is responsable to communicate with the cluster server. User case:In the deployment file there are mainly two part:Pod has one or several container image. User can set the replicas number. If a worker node die, the cluster server will be noticed and send a new task with losed job to living workers.
#### [Lab: Swarm Mode Introduction(40min)](https://training.play-with-docker.com/ops-s1-swarm-intro/) 
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/6Swarm(1).png)  
&lt;LAB-SWARM simple application deployment-before&gt;  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/7Swarm(2).png)  
&lt;LAB-SWARM simple application deployment-after&gt;  
After you get ready in docker swarm. Code below can create a basic distributed voting application.
```bash
git clone --depth 1 https://github.com/docker/example-voting-app 
cd example-voting-app  
cat docker-stack.yml  
docker stack deploy --compose-file=docker-stack.yml voting_stack`  
docker stack ls`  
docker stack services voting_stack  
docker service ps voting_stack_vote  
docker service scale voting_stack_vote=5    
```  
#### [AWS Tutorial: Break a Monolith Application into Microservices(40min)](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/?trk=gs_card)  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/8ECR.png)  
&lt;Last-Toturial figure&gt;  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/12deploy.png)

## Area 2 Big Data and Machine Learning
### Beginner Level: 
#### [Video: Hadoop Intro(20min)](https://www.youtube.com/watch?v=jKCj4BxGTi8&feature=youtu.be)  
Mapreduce:  
process amount data in parallel on large cluster  
job use map tasks to split input data-set.  
Framework sort outputs of the map as the input of reduce tasks.  
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/hadoop1.png)  

#### [QwikLab: Analyze Big Data with Hadoop(40min)](https://awseducate.qwiklabs.com/focuses/19?parent=catalog)  
`I have read the paper of HDFS and get a deep understanding about HDFS and HADOOP,Here is my Note`
HDFS is highly fault-tolerant and is designed to be deployed on low-cost hardware.  
**High falt-tolerant:**  
Therefore, detection of faults and quick, automatic recovery from them is a core architectural goal of HDFS.  
**Large thoughput:**  
The emphasis is on high throughput of data access rather than low latency of data access.  
**Large data set:**  
It should provide high aggregate data bandwidth and scale to hundreds of nodes in a single cluster.  
Reading more than writing HDFS applications need a write-once-read-many access model for files. This assumption simplifies data coherency issues and enables high throughput data access.  
Computation closer to data The assumption is that it is often better to migrate the computation closer to where the data is located rather than moving the data to where the application is running.  
**Portable:**  
HDFS has been designed to be easily portable from one platform to another.   
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/hadoop2.png) 
**Master/slave architecture:**  
*(1)* An HDFS cluster consists of a single NameNode, a master server that manages the file system namespace and regulates access to files by clients.  
*(2)* In addition, there are a number of DataNodes, usually one per node in the cluster, which manage storage attached to the nodes that they run on.   
*(3)* HDFS supports a traditional hierarchical file organization.   
*(4)* It stores each file as a sequence of blocks;all blocks in a file except the last block are the same size. The blocks of a file are replicated for fault tolerance.  
*(5)* The **NameNode** makes all decisions regarding replication of blocks.It periodically receives a Heartbeat and a Blockreport from each of the DataNodes in the cluster.A Blockreport contains a list of all blocks on a DataNode
cases, network bandwidth between machines in the same rack is greater than network bandwidth between machines in different racks.  
*(6)* For the common case, when the replication factor is three, HDFS’s placement policy is to put one replica on one node in the local rack, another on a node in a different (remote) rack, and the last on a different node in the same remote rack. The chance of rack failure is far less than that of node failure; this policy does not impact data reliability and availability guarantees. However, it does reduce the aggregate network bandwidth used when reading data since a block is placed in only two unique racks rather than three.  
*(7)* To minimize global bandwidth consumption and read latency, HDFS tries to satisfy a read request from a replica that is closest to the reader. If there exists a replica on the same rack as the reader node, then that replica is preferred to satisfy the read request. If angg/ HDFS cluster spans multiple data centers, then a replica that is resident in the local data center is preferred over any remote replica.  
**SAFEMODE:**   
A block is considered safely replicated when the minimum number of replicas of that data block has checked in with the NameNode. After a configurable percentage of safely replicated data blocks checks in with the NameNode (plus an additional 30 seconds), the NameNode exits the Safemode state.  
**The NameNode** uses a transaction log called the EditLog to persistently record every change that occurs to file system metadata.The NameNode uses a file in its local host OS file system to store the EditLog.
**FsImage:**  
The entire file system namespace, including the mapping of blocks to files and file system properties, is stored in a file called the FsImage. The FsImage is stored as a file in the NameNode’s local file system too.  
When the NameNode starts up, it reads the FsImage and EditLog from disk, applies all the transactions from the EditLog to the in-memory representation of the FsImage, and flushes out this new version into a new FsImage on disk. It can then truncate the old EditLog because its transactions have been applied to the persistent FsImage. This process is called a checkpoint. In the current implementation, a checkpoint only occurs when the NameNode starts up. Work is in progress to support periodic **checkpointing** in the near future.  
When a **DataNode** starts up, it scans through its local file system, generates a list of all HDFS data blocks that correspond to each of these local files and sends this report to the NameNode: this is the Blockreport.  
**HDFS communication protocols:**  
All HDFS communication protocols are layered on top of the TCP/IP protocol.  
**Remote Procedure Call (RPC):**  
A Remote Procedure Call (RPC) abstraction wraps both the Client Protocol and the DataNode Protocol. By design, the NameNode never initiates any RPCs.  
**Data Disk Failure, Heartbeats and Re-Replication:**  
The NameNode detects this condition by the absence of a Heartbeat message. The NameNode marks DataNodes without recent Heartbeats as dead and does not forward any new IO requests to them. The NameNode constantly tracks which blocks need to be replicated and initiates replication whenever necessary.  
Cluster Rebalancing （if space below threshold or high demand).  
The HDFS architecture is compatible with data rebalancing schemes. A scheme might automatically move data from one DataNode to another if the free space on a DataNode falls below a certain threshold. In the event of a sudden high demand for a particular file, a scheme might dynamically create additional replicas and rebalance other data in the cluster.  
**Data Integrity(verify data from matching checksums ):**  
When a client creates an HDFS file, it computes a checksum of each block of the file and stores these checksums in a separate hidden file in the same HDFS namespace. When a client retrieves file contents it verifies that the data it received from each DataNode matches the checksum stored in the associated checksum file. If not, then the client can opt to retrieve that block from another DataNode that has a replica of that block.  
**Metadata Disk Failure:**  
The **FsImage** and the **EditLog** are central data structures of HDFS. the NameNode can be configured to support maintaining multiple copies of the FsImage and EditLog.This synchronous updating of multiple copies of the FsImage and EditLog may degrade the rate of namespace transactions per second that a NameNode can support. However, this degradation is acceptable because even though HDFS applications are very data intensive in nature, they are not metadata intensive. When a NameNode restarts, it selects the latest consistent FsImage and EditLog to use.  
The NameNode machine is a single point of failure for an HDFS cluster. If the NameNode machine fails, manual intervention is necessary. Currently, automatic restart and failover of the NameNode software to another machine is not supported.(question)
**Snapshots:**  
Snapshots support storing a copy of data at a particular instant of time. One usage of the snapshot feature may be to roll back a corrupted HDFS instance to a previously known good point in time.  
**Data Organization:**  
**Data Blocks**  
A typical block size used by HDFS is 64 MB. Thus, an HDFS file is chopped up into 64 MB chunks, and if possible, each chunk will reside on a different DataNode.  
**Staging:**. 
In fact, initially the HDFS client caches the file data into a temporary local file. Application writes are transparently redirected to this temporary local file.When the local file accumulates data worth over one HDFS block size, the client contacts the NameNode. The NameNode inserts the file name into the file system hierarchy and allocates a data block for it. 
If a client writes to a remote file directly without any client side buffering, the network speed and the congestion in the network impacts throughput considerably.
**Replication Pipelining:**  
The first DataNode starts receiving the data in small portions (4 KB), writes each portion to its local repository and transfers that portion to the second DataNode in the list. The second DataNode, in turn starts receiving each portion of the data block, writes that portion to its repository and then flushes that portion to the third DataNode. Finally, the third DataNode writes the data to its local repository.  
**Accessibility:**  
HDFS can be accessed from applications in many different ways. Natively, HDFS provides a Java API for applications to use. A C language wrapper for this Java API is also available. In addition, an HTTP browser can also be used to browse the files of an HDFS instance. Work is in progress to expose HDFS through the WebDAV protocol.  
**FS Shell:**     
HDFS allows user data to be organized in the form of files and directories. It provides a commandline interface called FS shell that lets a user interact with the data in HDFS.  
### Conclusion
![image](https://github.com/XinShuYang/dist-sys-practice/blob/master/Image/hadoop3.png) 

> Include notes here about each of the links
