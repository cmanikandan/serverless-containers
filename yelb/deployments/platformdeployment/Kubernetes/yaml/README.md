These configurations had been tested against against Amazon EKS but should in theory work on any Kubernetes deployment. 

Some of these configurations (e.g. *alb*) is AWS specific in that it requires an ALB ingress controller.  

The picture below shows the layout of the Kubernetes deployment (with a load balancer) on top of EKS:

![yelb-eks-load-balancer](../../../../images/yelb-eks-load-balancer.png) 

**Deployment steps:**

1. Follow the steps mentioned in the EKS workshop to create a Cloud 9 workspace until the section on updating IAM settings for your Workspace - https://www.eksworkshop.com/020_prerequisites/workspace/

2. We will be using the [eksctl](https://eksctl.io/) to create the AWS Fargate on Amazon EKS cluster. Open a terminal window and issue the following commands.

```
echo 'export ALB_INGRESS_VERSION="v1.1.8"' >>  ~/.bash_profile
.  ~/.bash_profile

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin

eksctl version
```

3. Clone the git repo

```
git clone https://github.com/cmanikandan/serverless-containers
```

4. Create an Fargate EKS cluster by issuing the following commands 

```
eksctl create cluster \
  --name yelb-cluster \
  --region us-west-2 \
  --fargate
```

5. Deploy the ALB Ingress Controller on Amazon EKS - https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html. Sample deployment steps are given below, the steps assume that your EKS cluster is called **yelb-cluster** and it is deployed in **us-west-2**:

```
eksctl utils associate-iam-oidc-provider --region us-west-2 --cluster yelb-cluster --approve

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/iam-policy.json

aws iam create-policy \
    --policy-name ALBIngressControllerIAMPolicy1 \
    --policy-document file://iam-policy.json

kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/rbac-role.yaml

eksctl create iamserviceaccount \
    --region us-west-2 \
    --name alb-ingress-controller \
    --namespace kube-system \
    --cluster yelb-cluster \
    --attach-policy-arn arn:aws:iam::11xxxxxx:policy/ALBIngressControllerIAMPolicy1 \
    --override-existing-serviceaccounts \
    --approve

kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/alb-ingress-controller.yaml
```

Edit the ALB ingress controller and add the EKS cluster name, VPC ID and the AWS region that is deployed, as per Step 8 of https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html

```
kubectl edit deployment.apps/alb-ingress-controller -n kube-system
```

Confirm that the ALB Ingress Controller is running with the following command. 
```
kubectl get pods -n kube-system
```

6. Deploy the yelb application on the cluster

```
cd /home/ec2-user/environment/serverless-containers/yelb/deployments/platformdeployment/Kubernetes/yaml

kubectl apply -f yelb-k8s-ingress-alb-ip.yaml
```

7. Check the deployment and get the load balancer URL. You should be able to access the yelb application by opening this URL.

```
kubectl get pods
kubectl get svc
kubectl get ingress
```

