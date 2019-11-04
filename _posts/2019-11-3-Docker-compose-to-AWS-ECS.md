---
layout: post
title: Docker compose to AWS ECS
image: /images/2019Nov/compose-ecs.png
tags: ["tech", "work"]
---

## What are `Docker` and `Docker-compose` ? 

`Docker` is a popular technology that allows us to run applications in isolated containers. 
It is similar to the old concept of virtual machines but running faster and consume less computing power.  
`Docker-compose` is an awesome tool that helps us orchestrate multiple containers to work together as the whole application.
Having been using `Docker-compose` + `Docker` for a few years, I am still quite satisfied with the way it handles container's lifecycle and networking.

## What is `AWS ECS` ?
[`AWS ECS (Amazon Elastic Container Service)`](https://docs.aws.amazon.com/en_pv/AmazonECS/latest/developerguide/Welcome.html) is a container management service from Amazon. To explain it in simplest term, it is a more powerful, managed version of the [`Docker-Swarm`](https://docs.docker.com/engine/swarm/) + `Docker-Compose`. 

<img src="/images/2019Nov/ecs-overview.png" class="img-auto"/>

With that definition, shouldn't the conversion from `Docker-compose` to `AWS ECS` be painless ? Truth is Amazon's documentations are pretty overwhelming where not just `ECS` but a lot of other `AWS` Services are involved (`ECR`, `CloudWatch`, `EC2`, `Secret Manager`,  *optional*: `RDS`, `S3`, etc.). And when I've first started, I feel quite clueless. So this is to help any one who has the same problem.

## From `Compose` to `ECS`
At start, I've wasted quite some time just to understand how are all services on `AWS ECS` related to `Docker`. For those who are familiar with Docker, here are the direct mappings between AWS and Docker technologies.

<img src="/images/2019Nov/docker-vs-aws.png" class="img-auto"/>

### Why ?
Simple, because I wanted to tap on the power of other AWS Managed Services (like [`AWS WAF`](https://aws.amazon.com/waf/) [`AWS GuardDuty`](https://aws.amazon.com/guardduty/), [`Auto Scaling Groups`](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)). It's fine to run `Docker-compose` simply on a normal EC2 instance (just a normal Virtual machine), but we will miss out on other great protection and add on service from AWS.  
One important difference `Docker-compose` vs. `AWS ECS` is that `Docker-compose` is a self-contained service. You can define all the images you need + build image from your code. There is, however, no `build` in `AWS ECS`. 
### AWS Flow
If you have used `Docker service` and `Docker swarm` before, it is pretty similar. But if like me, you only used `Docker compose` before, then here how it looks like:

### 1. Building containers

### 2. Define task definitions
#### 2.1 Container images
#### 2.2 Define environments and secrets
#### 2.3 Networking

### 3. Define service and deploy