---
title: Docker and Containers - Break a Monolith Application into Microservices
date: "2018-11-06T22:12:03.284Z"
---

> This lab introduces an architectural evolvement of a simple web service application,
from monolith to microservice. Both architectures are containerized with Docker.
The lab also shows the advantages gained in system scalability by using microservice, which could be easily
achieved via changing some configurations in AWS console, and even without having to shut down the application.

## Learning Outcomes

- How to use aws-cli commands to set up host env via CloudFormation
- How microservices work and how to deploy them as cluster in Target Groups
- How to configure flow table entries in ALB (a software defined network)


## Interaction Logic of Components

- The Application Load Balancer (ALB) lets your service accept incoming traffic.
- Containerized microservers work in cluster as Target Groups, and ready to handle requests.
- ALB routes incoming requests to Target Groups based on match of path (forward or drop).

## Migration of Architecture

![deploy micro](https://d1.awsstatic.com/Developer%20Marketing/containers/5786%20Monolith%20Containers_6-switching-traffic.d54c5d1d16f4e86f2a712f862988fa05aa885b22.png)

- At first (1), the web service application is monolithic, which is accessible via a generic target group _api_, where ALB routes all HTTP requests to.
- Then (2) (3) we will break the application into 3 smaller but independent services, with each one containerized. 3 new target groups are created and
linked to the microservices, and ALB routes requests to them according to pre-defined patterns of path.
- Finally (4), the monolithic application is shut down, and the entire application is migrated to microservice architecture from monolithic.

## Prerequisites

- aws-cli
- Docker ce
- git

## Operations

### [Module One - Containerize the Monolith](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/module-one/) 

![monolith](https://d1.awsstatic.com/Developer%20Marketing/containers/monolith_1-monolith-microservices.70b547e30e30b013051d58a93a6e35e77408a2a8.png)

#### Preparations

- Clone demo from Github

```
git clone --depth 1 https://github.com/awslabs/amazon-ecs-nodejs-microservices.git
```

- Create ECS repository _api_ in ECS console

name: api

- Create IAM group and user to access ECS repository

Grant full access of container registry to group, then add user in group.

![group policy](group_policy.png)

- Configure AWS with User Keys

Get _AWS Access Key ID_ and _AWS Secret Access Key_ from last step, then

```bash
aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json
```

- Docker login

```
aws ecr get-login --no-include-email --region [region]
```

See Docker login information output, copy and paste to terminal to login ECS repository

```
docker login -u AWS -p ...
```

#### Build and Push

- Build Monolithic Docker Image of Service _api_

```
cd ~/amazon-ecs-nodejs-microservices/2-containerized/services/api
docker build -t api . 
```

- Tag Image with _v1_

```
docker tag api:latest [account-id].dkr.ecr.[region].amazonaws.com/api:v1
```

- Push Image to ECS Repository

```
docker push [account-id].dkr.ecr.[region].amazonaws.com/api:v1
```

Check in console, monolith is containerized.

![ecr repo](ecr_repo_create.png)

### [Module Two - Deploy the Monolith](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/module-two/)

![deploy monolith](https://d1.awsstatic.com/Developer%20Marketing/containers/monolith_4-Monolith-Architecture.4a066ba9d767e193cdc98c2a1735737de67ff3c4.png)

#### Launch an ECS Cluster using AWS CloudFormation

- Build Stack _BreakTheMonolith-Demo_ with YAML file

```
aws cloudformation deploy \
--template-file ~/amazon-ecs-nodejs-microservice/2-containerized/infrastructure/ecs.yml \
--region <region> \
--stack-name BreakTheMonolith-Demo \
--capabilities CAPABILITY_NAMED_IAM
```

In 5 minutes, see stack ready in CloudFormation console,

![create stack](create_stack.png)

#### Write a Task Definition

The task definition tells Amazon ECS how to deploy your application containers across the cluster.

- Create a new task definition, remember to choose __EC2__ as launch type
- Task Name, Container name: __api__
- Image: __[account-id].dkr.ecr.[region].amazonaws.com/api:v1__
- Port Mapping: __host 0, container 3000__

![task type](task_type_ec2.png)

#### Configure the Application Load Balancer: Target Group

The Application Load Balancer (ALB) lets your service accept incoming traffic. 
The ALB automatically routes traffic to container instances running on your cluster using them as a target group.

- Create and Configure the ALB Target Group
    - Name: __api__
    - Protocol: __HTTP__
    - Port: __80__
    - VPC: __Use the one created by Cloud Formation, Not the default VPC__

![create target group](create_target_group.png)

#### Configure the Application Load Balancer: Target Listener

The listener checks for incoming connection requests to your ALB, and routes them
to service groups.

- Add a Listener to the ALB
    - Protocol: __HTTP__
    - Port: __80__
    - Default target group: __api__

![create target listener](create_target_listener.png)

#### Deploy the Monolith as a Service

- Create Service on _BreakTheMonolith-Demo-ECSCluster_
    - Service name: __api__
    - Number of tasks: __1__
    - ELB Type: __Application Load Balancer__
    - IAM role: __BreakTheMonolith-Demo-ECSServiceRole__
    - Load Balancer ELB name: __demo__

- Add Service to Target Group
    - Listener port __80:HTTP__
    - Target group name: __api__

![create service](create_service.png)

#### Test your Monolith

- Visit DNS name of load balancer

You should see a message 'Ready to receive requests'.

- See Each Part of the Service
    - http://[DNS_name]/api/users
    - http://[DNS_name]/api/threads
    - http://[DNS_name]/api/posts

### [Module Three - Break the Monolith](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/module-three/)

![break down monolith](https://d1.awsstatic.com/Developer%20Marketing/containers/monolith_5-Microservices-app-architecture.a0103d1b39c5702fb94cfa20ec7fa29f1bb75e1f.png)

In this module, you will break the node.js application into several interconnected services and push each service's image to an Amazon ECR repository.

ALB routes requests to 3 different target groups which are associated with _microservice_, whose instances are represented in Docker containers.

#### Provision the ECR Repositories

- Service _api_ is broken into 3 microservices,
    - users
    - threads
    - posts

- Create Image Repository for Each Microservice

#### Build and Push Images for Each Service

- Build and Push 3 New Images

```bash
cd ~/amazon-ecs-nodejs-microservices/3-microservices/services
docker build -t [service-name] ./[service-name] 
docker tag posts:latest [account-id].dkr.ecr.us-west-2.amazonaws.com/posts:v1
docker push [account-id].dkr.ecr.[region].amazonaws.com/[service-name]:v1
```

![ecr 3 repos](ecr_create_3repos.png)

### [Module Four - Deploy Microservices](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/module-four/)

In this module, you will deploy your node.js application as a set of interconnected services behind an Application Load Balancer (ALB). 
Then, you will use the ALB to seamlessly shift traffic from the monolith to the microservices.

#### Create Task Definition for Each Microservice

- Create as in task _api_ for,
    - users
    - threads
    - posts

#### Create Target Groups for Each Microservice

- Plus  a 'dummy' target: __drop-traffic__

![new groups](https://d1.awsstatic.com/Developer%20Marketing/containers/4.2-targets.e39a29c6ca46d303d398dc08ecb883aab0d08aa6.png)

#### Create Listener Rules for Each Microservice

- Criteria: __IF Path = /api/[service-name]* THEN Forward to [service-name]__

![5 rules](5rules.png)

#### Deploy Microservices

- Deploy on cluster as _api_ for,
    - users
    - threads
    - posts

- See tasks running,

![running tasks](running_tasks.png)

#### Validate Deployment

- Same way as in testing monolith deployment

- Visit DNS name of load balancer

You should see a message 'Ready to receive requests'.

- See Each Part of the Service
    - http://[DNS_name]/api/users
    - http://[DNS_name]/api/threads
    - http://[DNS_name]/api/posts

## Official Links

[AWS Tutorial: Break a Monolith Application into Microservices](https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/?trk=gs_card)