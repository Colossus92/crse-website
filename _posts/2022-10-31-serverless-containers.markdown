---
layout: post
title:  "Save time with serverless containers"
date:   2022-10-31 08:00:00
header: "/assets/221031-serverless/aws_fargate.webp"
---
# Safe time with serverless containers
<div style="custom-justify-center">
    <img src="/assets/221031-serverless/header.png" alt="aws fargate" />
</div>


As a software developer, chances are you’re working on a side project, experimenting with new technology, or building a Proof of Concept for a customer.

When you want the application to become publicly available, there are several options. You could manage a server at home, configure a virtual server on one of the major cloud platforms, or even create a Kubernetes cluster with Amazon EKS.

But all these options could become time-consuming projects on their own. What if you want to bring your containerized application online without too much effort? Then AWS Fargate might be the perfect solution for you.
AWS Fargate

Fargate is one of the serverless services that AWS offers. That means that it’s possible to run containers without worrying about servers. There are a few steps to configure a deployment and make choices concerning resources, network, and IAM policies. After that, with one push of a button, your PoC is publicly available. In this step-by-step guide, we’re going to show you exactly how you can do this.
Prerequisites 

* <a href="https://repost.aws/knowledge-center/create-and-activate-aws-account" target="_blank" class="white-link">An activated AWS account</a>
* We’ll use <a href="https://hub.docker.com/r/nginxdemos/hello" target="_blank" class="white-link">this docker image</a>, but you can of course use your own 
* 10 minutes of your time

We’ll stay within the Free Tier, so no charges will be made for the actions taken for this demo.

## 1. Create an AWS ECS Cluster

Make sure you are logged into AWS. On the AWS home page type ECS into the quick search bar and select _Elastic Container Service_.

When on the home page of Amazon ECS, click on _Clusters_ in the left menu and then on the big blue button saying _Create Cluster_. This cluster is nothing like a Kubernetes Cluster with multiple nodes and a controlplane. All of that is taken care of for you! This cluster is just a logical grouping of _services_ and _tasks_.

| ![Amazon ECS Clusters](/assets/221031-serverless/1.create-cluster.webp) |
|:--:|
| Amazon ECS Clusters |

<br />
In the next form, type in a cluster name. We’re going for the most simple configuration, so the checkboxes for Enable Container Insights and Create VPCcan be left unchecked. Click on Create.
Confige your Fargate Cluster
![Configure cluster](/assets/221031-serverless/2.configure-cluster.webp)

If everything went well you’ll get a notification saying ECS Cluster created and it’s possible to view that cluster.

| ![View cluster](/assets/221031-serverless/3.view-cluster.webp) |
|:--:|
| Configure your Fargate Cluster |

<br />
Now you have created your first Fargate Cluster it’s good to mention the most important parts that will make your application run.
![Fargate Entities](/assets/221031-serverless/4.fargate-entities.webp)

### Container Definition
The most granular part is the Container Definition. In this definition you specify the source of the container image. This can be any registry, for instance Docker Hub or AWS Elastic Container Registry. Also health checks, environment variables, storage and a lot more can be configured on this level.

### Task Definition
A task definition is what encapsulates one or more container definitions. The available resources (CPU and memory) are configured in a task definition. Added, an IAM role can be assigned to enable the task to interact with other AWS services. If you’re familiar with Kubernetes then a Task Definition compares to a Kubernetes pod.

### Services
The job of ECS Services is to manage tasks, the instantiations of task definitions. The amount of running tasks is specified in a service. So when starting a service, it takes care of starting the appropriate number of tasks and making sure that the amount of tasks is available. So when an instance fails it will get rid of it and start a new one. Services are similar to a Kubernetes Deployment.

## 2. Create a new Task Definition

The next step is to create a Task Definition. Click on the left, on Task Definitions and next on the blue button saying _Create new Task Definition_.

![Create task definition](/assets/221031-serverless/5.create-task.webp)

Step 1 is to select a launch type compatibility. Here choose **FARGATE** as launch type compatibility and click on Next Step.

The second step is to configure the task and container definition.

* **Task definition name:** pick an appropriate name for your task definition
* **Task role:** leave this field empty
* **Operation system family:** pick Linux
* **Task execution role:** pick the default ecsTaskExecutionRole
* **Task memory (GB):** when following along with the default docker image, 1 GB is sufficient
* **Task CPU (vCPU):** 0,5 vCPU is enough

Now it’s time to configure the _Container Specification_. Click on _Add Container_ and enter the following values on the form.

* **Container name:** pick a name for the container
* **Image:** the path to the docker image you want to use. The path for this demo’s docker image is docker.io/nginxdemos/hello:**0.3
* **Memory Limits (Mib):** Soft limit, 1024
* **Port mappings:** The port where the container should receive traffic. Fill in 80 tcp for the demo’s docker image

All the other fields we can leave as is, so click on _Add_.

## 3. Run your application!

Now we’re ready to run your application. Head back to the cluster and click on Run new Task on the Task tab.

| ![Run task](/assets/221031-serverless/6.run-task.webp) |
|:--:|
| Run new task |

<br />
You will be presented with the last form we need to fill out. Choose the following values:

* **Launch type:** FARGATE
* **Operating system family:** Linux
* **Task definition Family:** the name of your Task Definition
* **Task definition Revision:** pick latest
* **Cluster VPC:** choose the default VPC with CIDR 172.31.0.0/16. If you’re using your own VPC, make sure a public subnet exists within your VPC
* **Subnets:** pick a random subnet. All subnets in default VPC are public
* **Auto-assign public IP:** make sure this is ENABLED

Finally, click _Run Task_.

## 4. Wait for your application to be available

You will be returned to the overview of your cluster. If all went well your task has status _PROVISIONING_.
![Task provisioning](/assets/221031-serverless/7.task-provisioning.webp)

Wait a couple of minutes before the task has the status _RUNNING_.
![Task running](/assets/221031-serverless/8.task-running.webp)

Click on the task to access the application. Copy the **Public IP** and navigate to http://\<public_ip>/apath?param=hello. So in this case http://3.90.42.90/apath?param=hello, but your assigned public IP will be different.
![Running app](/assets/221031-serverless/10.running-app.webp)

And see, your own application live within 10 minutes with AWS Fargate!

Thanks for reading this step-by-step guide. If you deployed your application with AWS Fargate, or if you have any tips or tricks please let us know in the comments below!