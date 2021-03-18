---
title: "AWS ECS"
date: "2021-03-03"
menu:
  sidebar:
    name: AWS ECS
    identifier: aws-ecs
    parent: aws
    weight: 10
---
# Amazon ECS

## Overview
- ECS clusters are logical grouping of EC2 instances
- EC2 instances run the ECS agent(Docker container)
- ECS agents registers the instance to ECS cluster
- EC2 instances run a special AMI made specifically for ECS

## Creating a ECS cluster
- Management console-> ECS
- Create cluster- providing details like the instance size, VPC and subnet details, security group details and so on
- We can see the ECS details, auto scaling groups, launch configuration and so on
- If we had enabled ssh to ec2 instance, we can ssh and see in the  docker logs that ec2 instance will be registering itself to ecs cluster that we created

## ECS task definitions
- It is metadata in JSON form that tells ECS how to run a Docker container
- It contains following information:
    -    Image name
    -    Port binding of container and host
    -    Memory and CPU required
    -    Env variables
    -    Networking information
    -    etc

## Creating an ECS task definition
- Go to ECS -> task definition -> create task definition
- Reserve memory and CPU size
- Provide the image+tag name
- Add container
- Set the hard, soft limit for the memory
- Port mapping , volume mapping, etc
- We can also see the task definition in JSON form as well

## Creating an ECS service
- It helps in defining how the tasks should be run and number of tasks should run
- ECS clusters-> cluster-> create service
- Choose the task, cluster, etc and create the service
- This should start running the task and can be visualised through docker logs in the host
- We can also scale the number of tasks to be run( if the port is reserved for a task, we need to scale instance number in auto scaling group too)

## ECS service with LB 
- We can have multiple instances running tasks and a load balancer can be used to stream the traffic among them
- Note: A service can't be edited to add load balancer, so a new service should be created with a load balancer attached to it
- Create a load balancer with appropriate vpc, subnets, security group and set the target group(set of instances)
- Now create a service in the cluster with an ALB attached.
- Allow security group of the cluster to have an inbound rule from ALB, with this we should be able to make call from ALB's DNS name

## ECR
- We can use AWS's own image repository for storing and retreiving images
- It can be made public or private
- In ECS-> ECR-> Create repository and name it
- Follow the instructions given to push your name from local

## Using our ECR image
- We can update our task to new revision and provide our image URL
- If we update our service with new revision, tasks in instances should restart with our image

## AWS Fargate
- This is a way of having ECS cluster without using EC2 instances
- It's managed by AWS
- We just have to increase and decrease our tasks, as needed

## Creating ECS cluster with Fargate and more
- ECS cluster-> create cluster using Fargate
- Now create a task with type Fargate
- Note: Port mapping is not necessary in this case, it does on it's own, just the container exposed port should be given
- Create a service with task created, assign a load balancer, target group, vpc and sec group
- ECS cluster is ready

## Placement strategy
- The way in which an EC2 task is placed on an instance with constraints of CPU, memory, available port
- Task placement strategy and task placement constraint helps us with this
- Note: This is only for EC2 type cluster
- Process taken by ECS to place task on an instance:
    - Idenity instances that satisify memory, cpu and port constraints
    - Find the instances that pass placement constraint
    - Find the instances that pass placement strategy
    - place the task

## Types of placement strategy
1. Binpack
    - Minimizes the cost as it chooses based on least amount of memory or CPU available(so less number of instances)

2. Random
    - Place tasks randomly to instances

3. Spread on AZ
    - Spreads tasks evenly on all AZs

4. Mix of them
    - Combinations like (spread on AZ, binpack) and (spread on AZ, spread on instances) are possible too

## Types of placement constraints
1. DistinctInstance 
    - Tasks _must_ be placed on distinct instances

2. Member of
    - An expression can be used to check if an instance belongs to that category. e.g, size of the instance is *micro*? and so on


## Applying constraints and strategy
- ECS-> cluster-> create service-> placement strategy

## ECR pricing
- https://aws.amazon.com/ecr/pricing/

## Image policy in ECR
- We can delete Images in ECR based on the criteria of number of days elapsed after the image is pushed

