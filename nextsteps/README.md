# Next steps

## 1. Using Spot Instances 

Utilizing Fargate Spot instances for deploying the yelb application. If you are familiar with EC2 Spot Instances, the concept is the same. AWS uses spare capacity in the AWS cloud to run the Fargate tasks. When the capacity for Fargate Spot is available, you will be able to launch tasks based on your specified request. When AWS needs the capacity back, tasks running on Fargate Spot will be interrupted with two minutes of notification. [Price (per CPU-Hour and GB-Hour) of a Spot Task is variable, ranging between 50% to 70% off the price of an On-Demand Task](https://aws.amazon.com/blogs/compute/deep-dive-into-fargate-spot-to-run-your-ecs-tasks-for-up-to-70-less/). 

Capacity providers are a new way to manage compute capacity for containers. For Fargate, the capacity providers are FARGATE and FARGATE_SPOT capacity providers. Let us add these capacity providers to the yelb-cluster that we just created.

```
aws ecs put-cluster-capacity-providers \
--cluster yelb-cluster \
--region us-west-2 \
--capacity-providers FARGATE FARGATE_SPOT \
--default-capacity-provider-strategy \
capacityProvider=FARGATE,weight=1 \
capacityProvider=FARGATE_SPOT,weight=3
```

What the above means that, we use a combination of FARGATE_SPOT and FARGATE capacity providers. We selected a Weight of 3 for FARGATE_SPOT and 1 for FARGATE. This means that for every four Tasks, three are started on FARGATE_SPOT and one on FARGATE. To test this, you can go and update any ECS service like the yelb-ui service and increase the number of tasks. You can now check, that the Fargate tasks will be running in a mix of Spot and On-demand Fargate providers. 

Note: Fargate Spot is not yet available for AWS Fargate on Amazon EKS. 

## 2. Using App Mesh

Services meshes such as AWS App Mesh help you to connect services, monitor your application’s network, and control the traffic flow. When an application is running within a service mesh, the application services are run alongside proxies which form the data plane of the mesh. The microservice process executes the business logic and the proxy is responsible for service discovery, observability, network encryption, automatic retries, and traffic shaping. 

You can AWS App Mesh with Amazon EKS as well as Amazon ECS. Please check the following blogs for leveraging App Mesh while running the yelb application on these two orchestration engines:

1. https://aws.amazon.com/blogs/containers/service-connectivity-inside-and-outside-the-mesh-using-aws-app-mesh-ecs-fargate/ 
2. https://aws.amazon.com/blogs/containers/getting-started-with-app-mesh-and-eks/

