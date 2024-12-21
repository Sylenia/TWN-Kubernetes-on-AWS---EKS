# Create EKS Cluster with Fargate Profile

## Project Overview

This demo project focuses on utilizing AWS Fargate with Amazon EKS to deploy an application. While the EKS cluster, node groups, and roles were created in previous projects, this guide will demonstrate creating Fargate IAM roles, configuring a Fargate profile, and deploying an example application using the Fargate profile.

---

## Technologies Used
- **Kubernetes**
- **AWS EKS (Elastic Kubernetes Service)**
- **AWS Fargate**

---

## Why Use Fargate in EKS?
AWS Fargate simplifies Kubernetes operations by eliminating the need to manage infrastructure for running pods. It abstracts the underlying compute layer, enabling a serverless Kubernetes experience.

### Benefits:
- **Scalability:** Automatically scales the compute resources based on workload demands.
- **Cost Efficiency:** Pay only for the resources your pods consume.
- **Security:** Provides isolated compute environments, enhancing workload security.
- **Operational Simplicity:** Reduces operational overhead associated with managing EC2 instances or Auto Scaling Groups.

### Best Practices:
- Use Fargate for bursty, stateless workloads.
- Segregate workloads by creating specific namespaces for Fargate usage.
- Apply security and resource policies to namespaces and pods for controlled resource utilization.
- Monitor application performance and resource costs with tools like Amazon CloudWatch and Kubernetes Metrics Server.

---

## Step-by-Step Guide

### 1. Create an IAM Role for Fargate

The Fargate role allows EKS to manage pods using AWS Fargate.

#### Command:
```bash
aws iam create-role \  
  --role-name eksFargatePodExecutionRole \  
  --assume-role-policy-document file://trust-policy.json
```

#### Attach the Policy:
```bash
aws iam attach-role-policy \  
  --role-name eksFargatePodExecutionRole \  
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSFargatePodExecutionRolePolicy
```

---

### 2. Create a Fargate Profile

Fargate profiles define how Kubernetes pods are scheduled on Fargate.

#### Command:
```bash
aws eks create-fargate-profile \  
  --cluster-name demo-cluster \  
  --fargate-profile-name demo-fargate-profile \  
  --pod-execution-role-arn arn:aws:iam::<ACCOUNT_ID>:role/eksFargatePodExecutionRole \  
  --selectors namespace=demo-namespace \  
  --subnets subnet-0123456789abcdef0 subnet-0987654321fedcba
```

---

### 3. Configure Pod Selection Rules

Use `selectors` to specify which pods should run on Fargate. For example, you can target specific namespaces and labels.

#### Example Selector:
```json
{
  "namespace": "demo-namespace",
  "labels": {
    "app": "nginx"
  }
}
```

---

### 4. Create and Configure a Namespace

Namespaces help segregate workloads for better resource management and security.

#### Command:
```bash
kubectl create namespace demo-namespace
```

---

### 5. Deploy an Example Application (Nginx)

Create and apply the Kubernetes manifest for Nginx.

#### Example Nginx Deployment YAML:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: demo-namespace
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.19.10
        ports:
        - containerPort: 80
```

#### Apply the Configuration:
```bash
kubectl apply -f nginx-deployment.yaml
```

---

## Validation Steps

1. **Verify Fargate Profile Creation:**
   ```bash
   aws eks describe-fargate-profile \  
     --cluster-name demo-cluster \  
     --fargate-profile-name demo-fargate-profile
   ```

2. **Check Pods Running on Fargate:**
   ```bash
   kubectl get pods -n demo-namespace -o wide
   ```

3. **Access the Deployed Application:**
   Expose the Nginx deployment using a LoadBalancer or ClusterIP service.
   
   #### Example Service YAML:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-service
     namespace: demo-namespace
   spec:
     selector:
       app: nginx
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
     type: LoadBalancer
   ```

   #### Apply the Service Configuration:
   ```bash
   kubectl apply -f nginx-service.yaml
   ```

---

## Cleanup Resources

To avoid unnecessary costs, delete the Fargate profile and associated resources when done.

#### Commands:
```bash
aws eks delete-fargate-profile \  
  --cluster-name demo-cluster \  
  --fargate-profile-name demo-fargate-profile

kubectl delete namespace demo-namespace
```

---

## References
- [AWS EKS Documentation](https://docs.aws.amazon.com/eks/)
- [AWS Fargate Documentation](https://docs.aws.amazon.com/fargate/)

Happy Deploying! 🚀🚀🚀
