# Distributed Systems Practice
Notes from learning about distributed systems in [GW CS 6421](https://gwdistsys18.github.io/) with [Prof. Wood](https://faculty.cs.gwu.edu/timwood/)

Before we go ahead and try to understand what AWS is and what services it offers, it’s very important to understand the basic core terms used in AWS. The oversimplified version of definition would be much easy for the very beginners who would like to know about this platform. 


**VPC (Virtual private cloud)**: It’s a dedicated section of cloud provided by AWS to all its users which isolates other users on same platform to access it <br>

**Aws EC2 (Elastic Cloud Compute)**:  It’s a virtual equivalent of the computer sitting in front of you, primarily used for processing power 

**Instance**:  It’s a dedicated piece of virtual computer provided to a customer <br>

**S3 (Simple Storage service)**: It is a cloud supported storage service provided by AWS <br>

**DynamoDB**: It’s a NoSQL database provided by AWS as a service <br>


## Cloud Web Apps
> **_Beginner Level_**

### [Launching a VM:](https://aws.amazon.com/getting-started/tutorials/launch-a-virtual-machine/) (24 min)
- After creation of account we are redirected to the home page where we can select different services offered by AWS
-	To launch VM we need to select EC2 service and press launch instance button
-	This will direct us towards the wizard where we can select machine image i.e operating system and its instance type i.e configuration of machine  
-	After reviewing we can directly launch our instance or configure it futhermore before launching it 
-	We will be prompted to choose an existing key pair or create a new key pair where we need to create new key and save .pem file in our user directory by creating a sub-directory called .ssh
-	to connect we need to open git bash and use command `ssh -i {full path of your .pem file} ec2-user@{instance IP address}` where IP address can be access once our instance is in running state
-	To terminate instance we need to go to the EC2 Console where in action button, navigate to Instance State, and click Terminate
-	Terminating instance after use is very important as we will be charged for the usage

### [Intro to S3:](https://awseducate.qwiklabs.com/focuses/30?parent=catalog) (30 min)
-	We need to click S3 from AWS management console to create a bucket, after clicking create bucket button, name your bucket and keep region as it is
-	We further need to configure bucket by selecting keep all versioning on and set permission as per the requirements (in manage public permission do not grant access to public) of the project then simply click create bucket  
-	To add files simply click your bucket name and then click upload where we can browse the files to be uploaded 
-	The file after uploading file, it will create an access link by clicking the link we won’t be able to access the file as the permission is set to private
-	 To change the permission follow the path  `S3>{bucketName}>{filename}>Permission>Public Access>Everyone`, this will allow us to access the file by clicking the link
-	We can also create bucket policy to assign set of permission to our bucket manually or by using policy generator 
-	To understand versioning system, add another file with name to the bucket and access it through the link where we will able to see new file which we recently uploaded
-	To access older version click on file name and click latest version to access all old versions

>**_Intermediate Level_**

### [Virtualization:](https://www.youtube.com/watch?v=GIdVRB5yNsk) (10 min)

Virtualization is known as an act of emulating a physically realized object where it provides the functionality of the object from which it was virtualized.  IBM in early 1960’s was not able to run few OS because of which they started to lose business. This encouraged them to take a step towards virtualization. Few smart people from Stanford university were able to perform software emulation who later established a company VMware. In todays world cloud technologies can sell a piece of computer by provided required number of core and memory. It is still known that we haven’t made the application which can utilize high numbers of core at a time.


### [Installing a LAMP Web Server on Amazon Linux 2:](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-lamp-amazon-linux-2.html) (40 min)
-	We need to launch and connect to Amazon Linux 2 instance as shown in above tutorial 
-	Now we need to download extra repo by running command`sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2` and then `sudo yum install -y httpd mariadb-server` command will install mariaDB on the instance which we have launched
-	`sudo systemctl enable httpd` command will start each system boot and to check if its enable run `sudo systemctl is-enabled httpd`
-	We need to add security rule to a ‘launch-wizard-N’ security group with following values:  type: HTTP, Protocol: TCP, Port Range:80, Source: Custom 
-	Type your public dns of your instance to web browser to test the server
-	We can create a file under /var/www/html/ directory and try to access it through public dns generated

### S3 versus an EC2 VM (20 min)

Amazon Elastic Cloud Compute (EC2) provides a scalable and elastic computer facility as a cloud web service. It is highly scalable service where as per the requirement we can easily scale up the capacity, at same time, it also elastic i.e. we can reduce the computing power as per the change of requirement. This service provides high configurational facility with various option of preexisting setups. However, EC2 is designed for providing processing power and ideally it recommended to be used as computing service. 

> Serving the content on EC2 :
-	We will be needed an additional service called EBS which provides storage capacity to EC2 instance
-	We will have to pay for whole block of EBS even if it is not in use fully
-	Multiple EBS volume can be attached to one instance
-	 It also provides instance storage which is recommended for temporary storage which changes quickly such as buffer, caches an scratch data etc 

Simple Storage Service (S3) is storage service provided by Amazon which in highly scalable in nature. It provided infrastructure and hardware to maintain and store data. It provides unlimited storage capacity where the limit of storage is very high which is hard to achieve. S3 supports various format which makes it easier for anyone to store multiple types of files. 

> Serving the content on S3:
-	It is highly scalable service
-	We need to pay according to size of the data we have stored i.e. pay as you go
-	It provided high reliability as data is duplicated to various servers
-	 Uptime of the servers is also very high 
-	It provides object level security apart from user level security 


### [Introduction to Amazon DynamoDB:](https://awseducate.qwiklabs.com/focuses/23?parent=catalog) (20 min)
-	DynamoDB is NoSQL database service provided by AWS
-	In AWS Management console we need to select DynamoDB under services where we can create a table by specifying table name and primary key
-	We can add data to table by clicking Create item under 'Items tab'
-	 To create additional attributes just click “+” sign in create item window
-	There are two ways to query table i.e. “Scan” and “Query” where we can provide query information and click start search which will give required output


### [Deploy a Node.js web app:](https://aws.amazon.com/getting-started/projects/deploy-nodejs-web-app/?trk=gs_card) (30 min)

-	To deploy the app, we can use 'Elastic Beanstalk' Environment where we can select the platform which matches language used in our application 
-	Select ‘Sample Application’ and launch the application by clicking ‘Create App’
-	Add permission to your environment instance by selecting “AmazonSNSFullAccess” in Roles page in IAM console which will give permission to the http requests to use AWS services
-	Deploy your application by uploading it in management page of Elastic Beanstalk console and your application will be up and running 
-	Information uploaded by the user in the app will be stored in DynamoDB table which is already created when we launch an app 
-	We can delete our app by selecting ‘Terminate’ in actions menu of Elastic Beanstalk console and we need to delete the table created in DynamoDB manually

### [Intro to AWS Lambda:](https://awseducate.qwiklabs.com/focuses/36?parent=catalog) (25 min)

-	AWS Lambda is a compute service which responds to the events by running associated code after event occurs
-	To use the service, we need to create functions by selecting Lambda from AWS services and add triggers once function is created
-	We can test the function by specifying events name and templet where we also have to modify our template according to the services we are using, e.g. by specifying bucket name created in S3
-	We can monitor different status of lambda function by selecting monitor tab by clicking name of the function we have created 

### [Intro to Amazon API Gateway:](https://awseducate.qwiklabs.com/focuses/21?parent=catalog) (15 min)
-	By using API Gateways in AWS we can create an application which uses microservices architecture 
-	To use API Gateway service, we can create a lambda function and integrate API Gateway
-	To create Lambda function we need to select AWS Lambda service and create function
-	We can configure the function according to our requirement and write function code
-	In designer section of Lambda we need to add trigger and configure it to add it in application
-	Our API is created, now we need to test our API which can be accomplish by clicking right arrow under API gateway section to view the details
-	Copy and paste API endpoint to new browser which will show us the entry we have added in function code
-	This proves that the part of the data requested was successfully shown on the browser which serves as the Client-Server model and it could be considered as a form of microservice architecture where we are using RESTful API and format of the API is JSON


### [Building a serverless web application:](https://aws.amazon.com/getting-started/projects/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/?trk=gs_card) (90 min) 

-	We are basically using various services offered by AWS to host a complete serverless web app on cloud
-	The services used in the application are DynamoDB, AWS Lambda, API Gateway, S3, Cognito
-	We will use S3 to host a static website where we need to enable website hosting in addition to the above tutorials mentioned on “Intro to S3” and index.html will always be the index document 
-	Amazon Cognito have two way of authenticating the user where user can use sign-up and sign-in functionality or use social identity provider such as Facebook, Twitter or Amazon
-	Note the pool id by creating a ‘User Pool’ and add your application through app client in general settings 
-	Update your config.js file where you need to add values for ‘userPoolId’, ‘userPoolClientId’ and ‘region’
-	We will have to create a DynamoDB table and IAM Role to provide various permission to the application
-	Later we must create Lambda function to handle requests where request details will be recorded in DynamoDB table and respond to the frontend application 
-	To use the function created in Lambda as a RESTFul API we will need to add API Gateway 
-	To authenticate we will need to add authorizers newly created API
-	Create a new resource method with ‘POST’ method and configure it to use Lambda function
-	Now we need to deploy the API and add ‘Invoke URL’ to config.js
-	Now simply test working of your application with website url created 
-	To destroy the application we need to delete S3 bucket, Cognito user pool, Lambda function, IAM Role, DynamoDB table, API and CloudWatch logs separately

### [Building a modern web application:](https://aws.amazon.com/getting-started/projects/build-modern-app-fargate-lambda-dynamodb-python/?trk=gs_card) (150 min)
-	We will be creating a web application using several different services of AWS, where application will be hosted on frontend web server and it will be connected to backend database
-	Create environment with Cloud9 AWS service by the name “MythicalMysfitsIDE” with default settings
-	Clone the Mythical Mysfits workshop repository with command `git clone --depth 1 https://github.com/aws-samples/aws-modern-application-workshop.git` and change directory to “aws-modern-application-workshop”
-	Create an S3 bucket with CLI command `aws s3 mb s3://mythical-mysfits-bucket-name` and configure it with `aws s3 website s3://YOUR_BUCKET_NAME --index-document index.html` command
-	By default, buckets are private and we need to change the policy to public with command `aws s3api put-bucket-policy --bucket mythical-mysfits-bucket-name --policy file://~/environment/aws-modern-application-workshop/module-1/aws-cli/website-bucket-policy.json`
-	Now we need to add content to S3 bucket using command `aws s3 cp ~/environment/aws-modern-application-workshop/module-1/web/index.html s3://REPLACE_ME_BUCKET_NAME/index.html `
-	We can test our configuration by typing following url ‘http://REPLACE_ME_BUCKET_NAME.s3-website-REPLACE_ME_YOUR_REGION.amazonaws.com’ with required values
-	We will create microservice architecture using AWS Fargate
-	We need to deploy cloud formation template with command `aws cloudformation create-stack --stack-name MythicalMysfitsCoreStack --capabilities CAPABILITY_NAMED_IAM --template-body file://~/environment/aws-modern-application-workshop/module-2/cfn/core.yml` 
-	It will create IAM Role, Security Group, Amazon VPC and few other settings 
-	Next, we will create a docker image by running command `docker build . -t REPLACE_ME_AWS_ACCOUNT_ID.dkr.ecr.REPLACE_ME_REGION.amazonaws.com/mythicalmysfits/service:latest` we can get account ID by `aws sts get-caller-identity` command
-	Test the service by running command `docker run -p 8080:8080 REPLACE_ME_WITH_DOCKER_IMAGE_TAG` where tag will be found just after installation of image
-	We further need to push the docker image to Amazon ECR by first creating repo using `aws ecr create-repository --repository-name mythicalmysfits/service` and then `$(aws ecr get-login --no-include-email)` which will automatically log us in
-	Push the image using `docker push REPLACE_ME_WITH_DOCKER_IMAGE_TAG` check it using `aws ecr describe-images --repository-name mythicalmysfits/service`
-	Command `aws ecs create-cluster --cluster-name MythicalMysfits-Cluster` will create fargate cluster and `aws logs create-log-group --log-group-name mythicalmysfits-logs` will create Cloudwatch Logs group
-	Change the values in task-defintion.json to register an ECS task definition and execute `aws ecs register-task-definition --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/task-definition.json` which will register a task
-	Create Network load balancer with `aws elbv2 create-load-balancer --name mysfits-nlb --scheme internet-facing --type network --subnets REPLACE_ME_PUBLIC_SUBNET_ONE REPLACE_ME_PUBLIC_SUBNET_TWO` and target group with `aws elbv2 create-target-group --name MythicalMysfits-TargetGroup --port 8080 --protocol TCP --target-type ip --vpc-id REPLACE_ME --health-check-interval-seconds 10 --health-check-path / --health-check-protocol HTTP --healthy-threshold-count 3 --unhealthy-threshold-count 3`  commands
-	Going forward create load balancer listener with `aws elbv2 create-listener --default-actions TargetGroupArn=REPLACE_ME,Type=forward --load-balancer-arn REPLACE_ME --port 80 --protocol TCP` command
-	Create a service linked role using `aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com`
-	Replace the indicated value in service-definition.JSON file and run command `aws ecs create-service --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/service-definition.json` also test the service using web browser
-	Replace myfitsApiEndpoint value in index.html with NLB URL  and upload the file using `aws s3 cp ~/environment/aws-modern-application-workshop/module-2/web/index.html s3://INSERT-YOUR-BUCKET-NAME/index.html` command
-	 We need to create CI/CD pipeline for that cerate a bucket using command `aws s3 mb s3://mythical-mysfits-artifacts-bucket-name` to store temporary artifacts
-	Modify artifacts-bucket-policy.json with ARN and newly created bucket name and run command `aws s3api put-bucket-policy --bucket mythical-mysfits-artifacts-bucket-name --policy file://~/environment/aws-modern-application-workshop/module-2/aws-cli/artifacts-bucket-policy.json` 
-	Create code commit repo with `aws codecommit create-repository --repository-name MythicalMysfitsService-Repository` 
-	Replace the values from the file code-build-project.json and run `aws codebuild create-project --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/code-build-project.json` command 
-	Update the code-pipeline.json and create pipeline in codepipeline by running `aws codepipeline create-pipeline --cli-input-json file://~/environment/aws-modern-application-workshop/module-2/aws-cli/code-pipeline.json` 
-	Update ecr-policy.json  and run `aws ecr set-repository-policy --repository-name mythicalmysfits/service --policy-text file://~/environment/aws-modern-application-workshop/module-2/aws-cli/ecr-policy.json` 
-	Run following command to configure git with Cloud9 IDE and clone repository

```
git config --global user.name "REPLACE_ME_WITH_YOUR_NAME"
git config --global user.email REPLACE_ME_WITH_YOUR_EMAIL@example.com
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
cd ~/environment/
git clone --depth 1 https://git-codecommit.REPLACE_REGION.amazonaws.com/v1/repos/MythicalMysfitsService-Repository
cp -r ~/environment/aws-modern-application-workshop/module-2/app/* ~/environment/MythicalMysfitsService-Repository/

```
-	Now push the changes using git push command
-	Its time to create DynamoDB table with command `aws dynamodb create-table --cli-input-json file://~/environment/aws-modern-application-workshop/module-3/aws-cli/dynamodb-table.json`
-	`aws dynamodb describe-table --table-name MysfitsTable` will give the details of table and `aws dynamodb scan --table-name MysfitsTable` will scan table 
-	Add the data using JSON file with command `aws dynamodb batch-write-item --request-items file://~/environment/aws-modern-application-workshop/module-3/aws-cli/populate-dynamodb.json`
-	Commit the changes and update website content  in S3 with `aws s3 cp --recursive ~/environment/aws-modern-application-workshop/module-3/web/ s3://your_bucket_name_here/` 
-	Next step will be adding user pool using `aws cognito-idp create-user-pool --pool-name MysfitsUserPool --auto-verified-attributes email` command
-	Create user pool client using `aws cognito-idp create-user-pool-client --user-pool-id REPLACE_ME --client-name MysfitsUserPoolClient` command
-	Create VPC link for REST API using` aws apigateway create-vpc-link --name MysfitsApiVpcLink --target-arns REPLACE_ME_NLB_ARN` CLI command
-	Update api-swagger.json file and run `aws apigateway import-rest-api --parameters endpointConfigurationTypes=REGIONAL --body file://~/environment/aws-modern-application-workshop/module-4/aws-cli/api-swagger.json --fail-on-warnings` 
-	Deploy the API using `aws apigateway create-deployment --rest-api-id REPLACE_ME_WITH_API_ID --stage-name prod` 
-	Now overwrite the exiting code base with command `cd ~/environment/MythicalMysfitsService-Repository/` and `cp -r ~/environment/aws-modern-application-workshop/module-4/app/* .`
-	Update index.html and run command `aws s3 cp --recursive ~/environment/aws-modern-application-workshop/module-4/web/ s3://YOUR-S3-BUCKET/`
-	Now new functionality will be added in our website
-	In the last part of the project we have to create CodeCommit repo by using `aws codecommit create-repository --repository-name MythicalMysfitsStreamingService-Repository` command and clone it to your IDE 
-	Copy streaming service code base using following command

```
cd ~/environment/MythicalMysfitsStreamingService-Repository/
cp -r ~/environment/aws-modern-application-workshop/module-5/app/streaming/* .
cp ~/environment/aws-modern-application-workshop/module-5/cfn/* .
```
-	`pip install requests -t .` will install dependencies 
-	The line from streamProcessor.py file needs to be replaced with our ApiEndpoint and push the changes
-	Create S3 bucket for lambda function code package and use `sam package --template-file ./real-time-streaming.yml --output-template-file ./transformed-streaming.yml --s3-bucket replace-with-your-bucket-name` command 
-	Deploy the stack using `aws cloudformation deploy --template-file /home/ec2-user/environment/MythicalMysfitsStreamingService-Repository/cfn/transformed-streaming.yml --stack-name MythicalMysfitsStreamingStack --capabilities CAPABILITY_IAM` command
-	Update the index.html and push new site to S3 using `aws s3 cp ~/environment/aws-modern-application-workshop/module-5/web/index.html s3://YOUR-S3-BUCKET/` command
-	Your app is up and running with all expected functionality
-	To clean up delete all resource and `aws cloudformation delete-stack --stack-name STACK-NAME-HERE` command will delete the stack



## Docker and Containers
> **_Beginner Level_**

### [Why docker?:](https://www.youtube.com/watch?v=RYDHUTHLf8U&t=0s&list=PLBmVKD7o3L8tQzt8QPCINK9wXmKecTHlM&index=23) (10 min)

There has been shifts thought the era of computers and in future we are more likely to shift to another technology as it happens. Docker is all about speed. Everyone in the industry would like to do things quickly which will give them enough time to spend on more important task or enhancement of the project. Docker makes it quicker to develop, build, test, deploy, update and recover the software. It includes almost all the software development cycle which it is why expected to bring the next computer shift in the world. A docker allows us to package the software and distribute it irrespective of the OS or the system we are using. It brings lots of flexibility in the whole process of software development.


### [DevOps Docker Beginners Guide:](https://training.play-with-docker.com/ops-s1-hello/)(35 min)

-	This DevOps beginners guide gives us brief insight into docker engine, containers, images and isolation 
-	A container is an application abstraction, where containers run on application layer and the focus is not so too much interaction with hardware
- This makes it boot faster and run the command and exit the container just like running command on normal terminal
-	 Isolation allows users to quickly create separate, isolated test copies of an application or service and have them run side-by-side without interfering with one another
-	We can run container with the command `docker container run hello-world` where it will check if docker has an image with name ‘hello-world’. If not present it will download from ‘Docker Registry’ and run it. Also `pull` command can be used to download image.
-	`docker container run alpine ls -l` command run alpine container and runs command `ls -l` in that container
-	We can also instantiate another container with same image with command like `docker container run alpine /bin/sh` where `docker container run -it alpine /bin/sh` will give you interactive shell where we can type command for that perticular instance
-	The command `docker container ls -a` will give us list of containers we instantiated before where we can select particular ‘Container ID’ and use it to access files or application stored or running on that container

>**_Intermediate Level_**

### [What are containers:]( https://www.youtube.com/watch?v=EnJ7qX9fkcU) (18 min)
-	A process can run inside OS which share many things such as memory, RAM etc
-	Container provides an isolated environment i.e a sandbox for a process and isolate it from other processes 
-	We can run one process per container where life cycle of process and container in which it is running is tightly couple
- It shows a parent child relationship with the help of the concept of image layering 
- We can create a snapshot of an image and add some more feature to shared image to create new snapshot
- Any (large) number of instances of containers can be made which includes all the prerequisite to run a process
- Containers doesn’t need to install dependencies separately for every instance  
- Docker host pull or push from image and other required files from registry which is replicated in image cache of docker
- Client can pull, create, run, commit etc to docker host with the help of host of daemon 
- Client can create a network virtualization in docker which can provide a functionality to processes to interact with each other
- Volume can be created to provide persistent storage area which remains active even after end of container instance 
- Also, to store a data we can send the data to network socket


### [Container vs VM:](https://www.youtube.com/watch?v=L1ie8negCjc)(10 min)

- Over the physical infrastructure there exists a hypervisor which gives a base to run multiple OS 
- Virtual hardware layer i.e.  limited number of NICS and Storage to which VM interacts
- We can size the VM where we are not bound by physical infrastructure 
- Docker is hosted by OS and container have its own dependencies with it as a docker files
- NIC, storage, size, kernel modules etc.  are provided via docker
- Containers provides abstraction by dividing OS itself whereas VM lies between physical module and OS 
- Sizing is fixed in container technology 
- Containers are fast as it contains minimum number of drivers and technology to run an instance


### [Docker Intro:]( https://training.play-with-docker.com/beginner-linux/) (30 min)

-	This tutorial video will guide on how to use simple docker command and build-ship-run workflow
-	`git clone --depth 1 https://github.com/dockersamples/linux_tweet_app` command will clone  a repository which we will be using later in the tutorial
-	`docker container run alpine hostname` will run hostname command inside alpine container
-	`docker container ls --all` will list all containers 
-	We can run ubuntu container inside alpine linux docker using `docker container run --interactive --tty --rm ubuntu bash` command which will also open interactive mode
-	Below command will start MySQL container
```
docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-pw \
 mysql:latest

```
-	We can check the status of our containers by using commands like `docker container logs` and `docker container top`
-	`docker exec -it mydb sh` command will give us shell inside already running MySQL container
-	We can check the version number from shell by using `mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version` command and exit the shell using `exit` command
-	We need to go to directory linux_tweet_app which we have downloaded using git command at very beginning also set the dockerid by using `export DOCKERID=<your docker id>` command
-	`docker image build --tag $DOCKERID/linux_tweet_app:1.0 .` will build docker image with name and version mentioned in the command
-	 Below command will the run the container which we have just build
```
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
```
-	We can check that the website will be up and running and to terminate use `docker container rm --force linux_tweet_app` command
-	We can modify image or files while running the app which using bind mount functionality by running following command
```
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \
 $DOCKERID/linux_tweet_app:1.0
```  
-	We can change the index.html and check the website or even update the image file using `docker image build --tag $DOCKERID/linux_tweet_app:2.0 .`  and check the website by refreshing the browser 
-	The changes will be applied even while its in running mode
-	We can upload our image by login into docker using `docker image push $DOCKERID/linux_tweet_app:1.0` command
-	We can find our uploaded images at `https://hub.docker.com/r/<your docker id>/ `

### [Doing more with docker images:]( https://training.play-with-docker.com/ops-s1-images/) (50 min)
-	Let’s start by running interactive shell in ubuntu `docker container run -ti ubuntu bash`
-	`apt-get install -y figlet` will install figlet on the system 
-	`docker container ls -a` will list all running containers and `docker container diff <container id>` will tell us difference between current container and changes made in the container
-	` docker container commit CONTAINER_ID` will commit the changes and will create image locally
-	`docker image tag <IMAGE_ID> ourfiglet` will add tag the image with name and we can check that from command `docker image ls`
-	To check container lets run `docker container run ourfiglet figlet hello` command and you will get the output
-	Create a file named index.js and add below lines into that
```
var os = require("os");
var hostname = os.hostname();
console.log("hello from " + hostname);
```
-	 Create another file named Dockerfile and add below details
```
FROM alpine
RUN apk update && apk add nodejs
COPY . /app
WORKDIR /app
CMD ["node","index.js"]

```
-	` docker image build -t hello:v0.1 .`  we can create our own image from 
-	` docker container run hello:v0.1` can be used to check if the container is working
-	We can add one line to index.js file by command ` echo "console.log(\"this is v0.2\");" >> index.js` and build new image using ` docker image build -t hello:v0.2 .`
-	Now we will pull image with ` docker image pull alpine` and inspect with ` docker image inspect alpine`
-	` docker image inspect --format "{{ json .RootFS.Layers }}" alpine` will get the list of layers 	
-	Now ` docker image inspect --format "{{ json .RootFS.Layers }}" <image ID>` will gives us more layers where sha256 code is same which is because new image is created from same alpine image

### [Lightboard VM vs containers:]( https://training.play-with-docker.com/ops-s1-swarm-intro/) (10 min)
-	Containers can be run inside virtual machine
-	Disk image of virtual machine includes user space program, kernel , inti systems, applications  etc. 
-	The size includes application and dependencies could be around hundreds of megabytes to tons of gigabyte 
-	In container size can range from tens of megabytes to gigabytes i.e. it is small
-	Size of the container could increase depending upon the process or application it has to serve
-	 If application needs user space tools and some other required files, we can create a new image based on existing image with new added requirements to create a new container 
-	Security in VMs is high as compared to security in containers
-	Boot time is usually high in VMs which can be improved using EFI and advance INIT systems 
-	In container setting up the sandbox is like kernel operation which makes it very fast to start a container

### [Docker networking:](https://training.play-with-docker.com/docker-networking-hol/) (50 min)
-	` docker network ls` command will list all the network establish with docker
-	` docker info` will give important information and list network driver plugin
-	By default, bridge docker container comes with bridge network `brctl show` will list the bridged on docker host, where, `ip a` can also show us the details of the bridge 
-	 Bridge network is default network i.e unless we specify all new containers will be connected to bridge network
-	We can create new container in sleep mode by ` docker run -dt ubuntu sleep infinity` command and verify by `docker ps`
-	`brctl show` will have new interface under bridge network which can also be seen by ` docker network inspect bridge`
-	Let’s check if container respond to the ping by `ping -c5 <ip address>`
-	` docker exec -it yourcontainerid /bin/bash` will start the bash inside container
-	We can redirect request using ` docker run --name web1 -d -p 8080:80 nginx` command which will transfer traffic from docker post 8080 to post 80 inside of container
-	Now we will initialize swarm by ` docker swarm init --advertise-addr $(hostname -i)` and it will output a command will should be run into other terminal to activate the swarm
-	` docker network create -d overlay overnet` command will create a overlay service which we can check by running `docker network ls` command
```
docker service create --name myservice \
--network overnet \
--replicas 2 \
ubuntu sleep infinity
``` 
-	Above command will start a service on two nodes and overnet network will be available in second terminal 
-	Open bash in your container and run below command where nameserver 127.0.0.11 will send DNS queries to embedded DNS resolver 
```
cat /etc/resolv.conf
search ivaf2i2atqouppoxund0tvddsa.jx.internal.cloudapp.net
nameserver 127.0.0.11
options ndots:0
```  
-	To remove everything run ` docker service rm myservice` and `docker kill container id`
-	Also remove both the nodes by `docker swarm leave --force` on both nodes


### [Swarm mode introduction:](https://training.play-with-docker.com/ops-s1-swarm-intro/) (40 min)
-	Swarm mode tell docker that many docker engines will be running and we need to coordinate operation between them. Usually, 3 or more manager nodes for several worker nodes are required in order to maintain high availability and scalability.
-	 ` docker swarm init --advertise-addr $(hostname -i)` will initialize a node as a manager
-	`docker node ls` will list the manager and worker nodes and show swarm members
-	` cat docker-stack.yml` download the github file and cat into the file using this command where services and its configuration ae mentioned
-	Deploy the services by 	` docker stack deploy --compose-file=docker-stack.yml voting_stack` command and check it by `docker stack ls` as stack is multiple services stacked together for deployment
-	` docker stack services voting_stack` this will give us details of each service and ` docker service ps voting_stack_vote` will list the vote service
-	` docker service scale voting_stack_vote=5` command will scale an application by 5 vote service


### [Kubernetes vs Swarm:]( https://www.youtube.com/watch?v=L8xuFG49Fac) (4 min)
- Swarm can enable to run containers in cluster
- it can do redeployment with 0 downtime
- it manages your container in production
- kubernetes is orchestration system for docker and we can use it for non-docker environment too
- Kubernetes have far more features and widely used tool as compared to built-in docker swarm
- Swarm is less powerful still very featureful


### [kubernetes in 5 min:]( https://www.youtube.com/watch?v=PH-2FfFD2PU) (5 min)

- We can enforce desire state management using kubernetes
- API rest within kubernetes cluster service 
- It is a system which helps us deploy desired configuration across our system
- kubelet process runs in all the host or workers which helps communicating with kubernetes
- the desired configuration is mentioned in .yaml file which is feeded to kubernetes 
- file is given to API and kubernetes cluster service will decide how to schedule pods in the environment
- if one worker goes down which will disturb the configuration which kubernetes will maintain by reconfiguring the whole system with available workers

### [Learn more about kubernetes on your own:]( https://kubernetes.io/) (40 min)

- Kubernetes is a production-grade, open-source platform that orchestrates 
the scheduling and execution of application containers within and across computer clusters.
- It contains master which manages the cluster and node which  is  physical computer which serves as worker machine
- Kubernetes gives a single DNS name for a set of containers, and can balance load across all containers
- To deploy containerized application over kubernetes cluster we need to create Kubernetes Deployment configuration
- Deployment controller monitors instances and if one of the instance goes down it replaces it. This provides a self-healing mechanism to address machine failure or maintenance
- We can use kubernetes command line interface called kubectl for deployment 
- Pods running in kubernetes will be in private mode and by default visible by only other pods
- We use API endpoint for communication with outside world

### Install Docker on a cluster of EC2 VMs and use Kubernetes to orchestrate them - 80 min 
- I followed the instructions and created EC2 instances
- I successfully installed kubernetes cluster on EC2 instance by following the kubernetes installation steps
- Cluster of containers is advantages in a way that failure of one instance does not affect the system
- We define the configuration .yml file which defines the behavior of the system
- I have terminated the instance after the completion of above steps

### [AWS Tutorial: Break a Monolith Application into Microservices:]( https://aws.amazon.com/getting-started/projects/break-monolith-app-microservices-ecs-docker-ec2/?trk=gs_card) (1.5 Hours)
- We will deploy a monolithic node.js application to a Docker container, then decouple the application into microservices without any downtime
- Install CLI and docker have text editor ready on your computer
- Download the project and create repo in AWS ECR
- Build the image and tag it to push it in AWS ECR
- Navigate to AWSCloudFormation to launch EC2 cluster
- Create task definition and configure application load balancer to deploy the monolithic app
- We need to create three repositories and build and tag images for all microservices 
- We need to write a task definition with given configuration and repeat step 3 times
- We then configure the target groups and listener rules
- Through cluster select your cluster and configure the service
- We then update listener rulers to re-route traffic to the Microservices
- Validate the deployment using appropriate links
- To clean up we must turn off the process and delete listener and target groups which we have created
- We then delete the CloudFormation Stack and deregister the task definition
- Finally delete all repositories which we have created 

## Big Data and Machine Learning 
> **_Beginner Level_** 

### [Big Data and Hadoop:](https://www.youtube.com/watch?v=jKCj4BxGTi8&feature=youtu.be) (20 min)
- Before year 2000 amount of data was less and data computation was dependent on available computers
- data started growing rapidly in uncontrolled manner without given enough computation power to process it
- Distributed system showed us a way to solve this problem where task divided to many computers takes less time
- There are still few problems associated with distributed system which can be overcome by using Hadoop technology
- Hadoop is reliable, scalable, economical and flexible 
- It has many components where HDFS is a storage layer for Hadoop
- HBase is NoSQL DB which stores data in HDFS
- Sqoop is a tool which transfers data from RDBM to HDFS and vice versa
- Streaming data or events data can be used instead of Sqoop
- Spark is open source cluster computing framework which is used for processing data
- Hadoop Map Reduce also process the data and commonly used framework
- Pig is used to 	converting script to analyze the data
- Impala is also can be used for analysis of the data which has low latency 
- HIVE is usually used for data processing and ETL
- Cloudera search can be used by non-technical user to access the data in Hadoop and HBase
- Oozie is a coordination system used to manage the Hadoop jobs
- Hue stands for Hadoop user experience which is web interface for Hadoop
- Data is ingested using Sqoop and Flume 
- Data is processed by MapReduce and Spark and stored using HBase and HDFS
- Impala, Hive and Pig is used for analysis of data
- We can access the data using Hue and Cloudera Search


### [Analyze Big Data with Hadoop:](https://awseducate.qwiklabs.com/focuses/19?parent=catalog) (40 min)

- Create an S3 bucket with name hadoop-{any number}
- Launch an EMR cluster with instance 2 and select S3 bucket which we have just created
- Select proceed without EC2 keypair and keep the permission custom
- Create a cluster with mentioned configuration
- In steps, add steps with type Hive Program and name it process log
- Select S3 location as `s3://us-west-2.elasticmapreduce.samples/cloudfront/code/Hive_CloudFront.q`
- And Input location as `s3://us-west-2.elasticmapreduce.samples`
- Output should be S3 bucket which we have created and click add
- Simply go to bucket we have created and click OS request folder to download the file in it
- File will have access request by OS
- Terminate your EMR cluster and end the lab

## [Technical Report Link on Hadoop](https://www.linkedin.com/pulse/unveiling-hadoop-basics-sarthak-poshattiwar/)
