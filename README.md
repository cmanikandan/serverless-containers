# Build an end to end serverless app based on containers on AWS Fargate for Amazon ECS and Amazon EKS


This repo contains the steps to run a sample application, on AWS Fargate - which is a serverless compute engine for containers that works with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS).

The sample application that we will be deploying is yelb - https://github.com/mreferre/yelb 

The video for the twitch live session has been now posted at https://www.twitch.tv/videos/715886887

 [![Twitch video link](https://github.com/cmanikandan/serverless-containers/blob/master/devax.png)](https://www.twitch.tv/videos/715886887)

## yelb tech stack:

There is a front-end component called yelb-ui that is responsable for vending the JS code to the browser. This code is compiled from an Angular 2 application. Depending on the deployment model this code can be served from an instance (EC2), from a container (Docker, Kubernetes, ECS) or from an S3 bucket (serverless). The yelb-ui component may also include, depending on the deployment model, an nginx proxy. In this session, we will be deploying the code on AWS Fargate on Amazon ECS and Amazon EKS.

The application component that exists is yelb-appserver. This is a Sinatra application that basically read and write to a cache server (redis-server) as well as a Postgres backend database (yelb-db). Redis is used to store the number of page views whereas Postgres is used to persist the votes.

**Prequisites:**

We will be using [Cloud 9](https://aws.amazon.com/cloud9/) as a developer desktop to deploy yelb. 
Note, building docker images can easily eat up your disk space on Cloud 9, its always a good practice to resize your disk volumes to a bigger size by following the steps outlined at - https://docs.aws.amazon.com/cloud9/latest/user-guide/move-environment.html


**Lets get started !!**

1. [Deployment of yelb on AWS Fargate on Amazon ECS](https://github.com/cmanikandan/serverless-containers/tree/master/yelb/deployments/platformdeployment/AWS/ECS)
2. [Deployment of yelb on AWS Fargate on Amazon EKS](https://github.com/cmanikandan/serverless-containers/tree/master/yelb/deployments/platformdeployment/Kubernetes/yaml)
3. [Next steps](https://github.com/cmanikandan/serverless-containers/tree/master/nextsteps)

Hooe you had fun, deploying on AWS Fargate !! 
