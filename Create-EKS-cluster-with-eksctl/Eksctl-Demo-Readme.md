# Demo Project: Create EKS Cluster with eksctl

## Technologies Used
- **Kubernetes**
- **AWS EKS**
- **eksctl**
- **Linux**

---

## Project Description
This project demonstrates how to create an Amazon EKS cluster using the `eksctl` tool. By leveraging `eksctl`, you can drastically reduce the manual steps involved in setting up an EKS cluster, allowing you to focus more on deploying and managing your Kubernetes workloads.

### Why Use `eksctl`?
Creating clusters from the command line with `eksctl` is more convenient because:
- It automates the creation of all necessary resources, such as VPC, subnets, and nodes.
- Saves time and reduces the risk of human error compared to manual setup through the AWS Management Console.
- Ensures consistent and reproducible cluster configurations, which are crucial for production environments.

---

## Installation Guide

### Prerequisites
Ensure the following tools are installed and configured:
- **AWS CLI**: To authenticate and interact with AWS services.
- **kubectl**: To manage your Kubernetes cluster.

### Installing `eksctl`
1. **Download the Latest Version**:
   ```powershell
   Invoke-WebRequest -Uri https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Windows_amd64.zip -OutFile eksctl.zip
   ```

2. **Extract the ZIP File**:
   ```powershell
   Expand-Archive -Path eksctl.zip -DestinationPath . -Force
   ```

3. **Move the Binary to a Directory in PATH**:
   ```powershell
   Move-Item -Path .\eksctl.exe -Destination C:\Windows\System32\
   ```

4. **Verify Installation**:
   ```powershell
   eksctl version
   ```

For Linux and macOS, refer to the official [eksctl installation guide](https://eksctl.io/). 

---

## Creating the Demo Cluster
Use the following command to create a demo EKS cluster:

```bash
eksctl create cluster \
  --name demo-cluster \
  --version 1.27 \
  --region eu-central-1 \
  --nodegroup-name demo-nodes \
  --node-type t2.micro \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3
```

### Explanation:
- `--name demo-cluster`: Sets the name of the cluster.
- `--version 1.27`: Specifies the Kubernetes version.
- `--region eu-central-1`: The AWS region where the cluster will be created.
- `--nodegroup-name demo-nodes`: Names the node group for worker nodes.
- `--node-type t2.micro`: Specifies the instance type for worker nodes.
- `--nodes 2`: Creates 2 worker nodes initially.
- `--nodes-min 1`: Minimum number of nodes in the auto-scaling group.
- `--nodes-max 3`: Maximum number of nodes in the auto-scaling group.

---

## Best Practices

### General Best Practices
1. **Use Tags**:
   Always tag your AWS resources for easier management and cost tracking.
2. **Enable Logging**:
   Configure cluster logging to monitor activity and troubleshoot issues.
3. **Version Management**:
   Stick to stable Kubernetes versions and update clusters regularly.

### Security Best Practices
1. **Least Privilege Principle**:
   Limit IAM permissions to only what is necessary for users and roles.
2. **Private Clusters**:
   Whenever possible, create private EKS clusters to limit exposure to the internet.
3. **Enable Encryption**:
   Use AWS KMS to encrypt Kubernetes secrets.
4. **Node Security**:
   - Use managed node groups to simplify updates and security patches.
   - Ensure nodes are regularly updated with security patches.

---

## Additional Notes
- **Cluster Clean-Up**: Remember to delete your cluster after testing to avoid unnecessary charges.
  ```bash
  eksctl delete cluster --name demo-cluster
  ```
- **Documentation**: For more advanced configurations, refer to the official [eksctl documentation](https://eksctl.io/).

---

Enjoy creating your clusters with ease and confidence using `eksctl`! 🚀🚀🚀
