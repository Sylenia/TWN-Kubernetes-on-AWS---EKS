# AWS EKS DevOps Project

This project demonstrates how to set up and deploy an Amazon Elastic Kubernetes Service (EKS) cluster with worker nodes, auto-scaling, and a sample application. The guide follows best DevOps practices for managing Kubernetes clusters in AWS.

---

## Table of Contents

1. [Technologies Used](#technologies-used)
2. [Project Overview](#project-overview)
3. [Prerequisites](#prerequisites)
4. [Steps to Execute](#steps-to-execute)
    - [Create IAM Role for EKS Cluster](#1-create-iam-role-for-eks-cluster)
    - [Create VPC for Cluster](#2-create-vpc-for-cluster)
    - [Create EKS Cluster](#3-create-eks-cluster)
    - [Create Node Group and Attach Worker Nodes](#4-create-node-group-and-attach-worker-nodes)
    - [Configure Auto-Scaling for Worker Nodes](#5-configure-auto-scaling-for-worker-nodes)
    - [Deploy Sample Application](#6-deploy-sample-application)
5. [Best Practices](#best-practices)

---

## Technologies Used
- **Amazon EKS**
- **Kubernetes**
- **AWS CloudFormation**

---

## Project Overview
1. Create necessary IAM roles.
2. Set up a VPC with CloudFormation templates for the cluster.
3. Deploy the EKS cluster and attach worker nodes.
4. Configure auto-scaling for the worker nodes.
5. Deploy a sample Nginx application to demonstrate functionality and auto-scaling.

---

## Prerequisites
- AWS CLI installed and configured.
- `kubectl` installed and configured.
- AWS IAM permissions for managing EKS resources.

---

## Steps to Execute

### 1. Create IAM Role for EKS Cluster
Create an IAM role to enable the EKS cluster to interact with AWS resources.

#### Commands:
```bash
aws iam create-role \ 
  --role-name EKS-Cluster-Role \ 
  --assume-role-policy-document file://eks-cluster-trust-policy.json

aws iam attach-role-policy \ 
  --role-name EKS-Cluster-Role \ 
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy
```

### 2. Create VPC for Cluster
The VPC must include public and private subnets, and it should be configured to allow Kubernetes to modify the configuration via IAM roles.

#### Steps:
1. Use a CloudFormation template to create the VPC.
2. Include both public and private subnets.

#### Commands:
```bash
aws cloudformation create-stack \ 
  --stack-name EKS-VPC-Stack \ 
  --template-body file://vpc-template.yaml \ 
  --capabilities CAPABILITY_NAMED_IAM
```

### 3. Create EKS Cluster
Manually set up the EKS cluster in the AWS Management Console:
1. Go to the EKS dashboard.
2. Create a new cluster and attach the IAM role created earlier.

#### Configure kubeconfig:
```bash
aws eks update-kubeconfig --name eks-cluster-test
```

### 4. Create Node Group and Attach Worker Nodes
Create a Node Group to add worker nodes to the cluster. Assign an IAM role for the worker nodes.

#### Commands:
```bash
aws eks create-nodegroup \ 
  --cluster-name eks-cluster-test \ 
  --nodegroup-name worker-nodes \ 
  --subnets subnet-XXXXXX subnet-YYYYYY \ 
  --node-role arn:aws:iam::XXXXXXXXXXXX:role/Node-Group-Role
```

### 5. Configure Auto-Scaling for Worker Nodes
Set up auto-scaling for worker nodes to ensure the cluster scales based on workload demands.

#### Steps:
1. Create a custom policy for the cluster role to allow auto-scaling.
2. Apply the Cluster Autoscaler YAML file:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```

3. Edit the YAML file to specify:
   - Kubernetes version
   - Cluster name

#### Example Edits:
```yaml
- --cluster-name=eks-cluster-test
- --kubernetes-version=1.22
```

### 6. Deploy Sample Application
Deploy an Nginx application to the EKS cluster to test the setup and demonstrate auto-scaling.

#### Steps:
1. Apply the `nginx-config.yaml` from the previous configuration.

#### Commands:
```bash
kubectl apply -f nginx-config.yaml
```

2. Increase replicas to test auto-scaling:
```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

## Best Practices
- **Security**: Restrict IAM role permissions to only what is necessary.
- **Networking**: Use private subnets for sensitive workloads.
- **Monitoring**: Implement CloudWatch metrics for visibility.
- **Scalability**: Leverage Kubernetes Horizontal Pod Autoscaler (HPA) alongside Cluster Autoscaler.
- **Version Control**: Always pin Kubernetes and AWS CLI versions to ensure compatibility.

---

Happy Deploying! 