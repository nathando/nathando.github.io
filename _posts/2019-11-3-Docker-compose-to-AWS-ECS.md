---
layout: post
title: Docker compose to AWS ECS Part 1
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

<img src="/images/2019Nov/aws-ecs-flow.png" class="img-auto"/>

In this part, we will go through just first step to build the container images. `Docker-compose` file is pretty self-contained that it can specify a direct build right inside where it is not possible for `AWS ECS Task`. So we start with build the images. 

### 1. Building images
Take an example of a Docker compose file with a few services.

{% highlight ruby %}
version: '2'
services:
    nginx:
        image: nginx:latest
        hostname: nginx
    redis:
        image: redis:latest
        hostname: redis
    web:
        build:
            context: .
            dockerfile: Dockerfile
        env_file: .envs/local.env
        command: ...
        ports:
            - "8000:8000"
       volumes:
        - .:/code
       depends_on:
        - nginx
        - redis
{% endhighlight %}
Only focus on those that we has to build first (in this case: `web` service). 
The rest can be easily specify in task definition (ECS equilvalent of Docker-compose file). AWS ECR (Elastic Container Repositories) is Docker Hub equivalent. Process to build and push images is totally similar: 

Create a repository and copy the image

After done, copy the url for later use. Your url will be in this format: 
**your-account-id**.dkr.ecr.**your-region**.amazonaws.com/**your-repo**:latest

Building the docker image:
`docker build -f Dockerfile -t .`

### 2. Define task definitions
Think of a ECS Task as a task (long running or one time) that is based on a the docker compose. 
Similar to how we call `docker-compose up`, we use `AWS ECS Service` to run `AWS ECS Task`

#### 2.1 Container images
For this part, we keep it simple first. In this section, we define which image repositories to pull and start container.
In part 2, we will look at how to make it check for healthchecks and container dependencies. 
Here is the example of the `main` container:  

{% highlight ruby %}
{
    "name": "web",
    "essential": false,
    "image": "your-account-id.dkr.ecr.your-region.amazonaws.com/your-repo:latest",
    "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
            "awslogs-group": "your-group",
            "awslogs-region": "your-region",
            "awslogs-stream-prefix": "your-prefix"
        }
    }
}
{% endhighlight %}

#### 2.2 Define environments and secrets
For each container, you will need some parameters to be passed into it as environments.
AWS divide them into 2 sections:  

- `environment`: list of environment variables you want to pass into container but not sensitive information
- `secrets`: same as above except that these are sensitive information

It is trivial to declare an **environment** section:

{% highlight ruby %}
{
    ...
    "environment": [
        {
            "name": "DJANGO_SETTINGS_MODULE",
            "value": "web.settings.staging"
        }
    ]
    ...
}
{% endhighlight %}

For **secrets** section, we need to define them as secure/encrypted strings using AWS console. 
To do this, go to your AWS Console, search for `System Manager`, click on `Parameter Store`

Create new secure string with properly scoped names like `/your-scope/PARAM_NAME` (your-scope can be your environments like staging, production, etc.):

<img src="/images/2019Nov/aws-param1.png" class="img-auto"/>

Enter value for the secret string and save  

<img src="/images/2019Nov/aws-param2.png" class="img-auto"/>

Once the param created, your `arn` will in the format of `arn:aws:ssm:your-region:your-account-id:parameter/your-scope/SECRET_KEY`

{% highlight ruby %}
{
    ...
    "secrets": [
        {
            "name": "SECRET_KEY",
            "value": "arn:aws:ssm:your-region:your-account-id:parameter/your-scope/SECRET_KEY"
        }
    ]
    ...
}
{% endhighlight %}

#### 2.3 Create task definition with `aws cli`
It is okay to create a Task Definition from console. 
However, I've found it much easier to create task definition using json. 
With all the section above, we can put everything together as a final json (saved as `task.json`)

{% highlight ruby %}
{
    "executionRoleArn": "arn:aws:iam::your-account-id:role/ecsTaskExecutionRole",
    "containerDefinitions": [
        {
            "name": "web",
            "essential": false,
            "image": "your-account-id.dkr.ecr.your-region.amazonaws.com/your-repo:latest",
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "your-group",
                    "awslogs-region": "your-region",
                    "awslogs-stream-prefix": "your-prefix"
                }
            },
            "environment": [
                {
                    "name": "DJANGO_SETTINGS_MODULE",
                    "value": "web.settings.staging"
                }
            ],
            "secrets": [
                {
                    "name": "SECRET_KEY",
                    "value": "arn:aws:ssm:your-region:your-account-id:parameter/your-scope/SECRET_KEY"
                }
            ]
        }
    ],
    "cpu": "1024",
    "memory": "1024",
    "family": "your-task-family"
}
{% endhighlight %}

With this json, we can run command to create a task with simple call to `aws cli`  

`aws ecs register-task-definition --cli-input-json file://task.json`

Note: the path needs to start with `file://` for aws command to be able to find the file.


With this, you will be able to run the task itself. Next part we will look at how to:  
- Link multiple containers together with networking and exposed ports
- Define healthchecks for containers so that we know if the container service is healthy
- Mount docker volumes and use them in containers
- Define service for the long running tasks and update the service with new task version