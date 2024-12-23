# Demo Project: CD - Deploy to LKE Cluster from Jenkins Pipeline

## Overview

This project demonstrates deploying a containerized application to a Linode Kubernetes Engine (LKE) cluster using a Jenkins pipeline. The steps include creating an LKE cluster, installing the required tools and plugins, and adjusting the Jenkinsfile for deployment.

## Technologies Used

- Kubernetes
- Jenkins
- Linode LKE
- Docker
- Linux

---

## Prerequisites

1. **Linode Account:** Ensure you have an active Linode account with access to create LKE clusters.
2. **Jenkins Server:** A running Jenkins instance with Docker installed.
3. **Kubernetes CLI Plugin:** Install the Kubernetes CLI plugin on Jenkins.
4. **kubectl Tool:** Ensure `kubectl` is installed and available in the Jenkins container.
5. **Application:** A containerized application ready for deployment.

---

## Project Steps

### 1. Create a Kubernetes Cluster on Linode

1. Log in to your Linode account.
2. Navigate to the Kubernetes section and create a new cluster.
3. Download the generated `kubeconfig` file once the cluster is created.

#### Best Practices:

- Use descriptive names for clusters and resources.
- Select a region close to your users to reduce latency.

---

### 2. Add LKE Credentials to Jenkins

1. **Navigate to Credentials:**

   - Jenkins Dashboard > Manage Jenkins > Manage Credentials.

2. **Add Secret File:**

   - Choose "Secret file" as the credential type.
   - Upload the downloaded `kubeconfig` file and provide a recognizable ID (e.g., `lke-kubeconfig`).

#### Best Practices:

- Use descriptive IDs for credentials.
- Rotate credentials regularly to maintain security.

---

### 3. Install Kubernetes CLI Plugin

1. Navigate to Jenkins Dashboard > Manage Jenkins > Manage Plugins.
2. Search for and install the **Kubernetes CLI** plugin.

#### Verify Installation:

- Restart Jenkins after installation.
- Ensure the plugin is available in the "Pipeline Syntax" configuration.

---

### 4. Configure Jenkinsfile to Deploy to LKE Cluster

Below is an example Jenkinsfile:

```groovy
pipeline {
    agent any

    environment {
        KUBECONFIG_CREDENTIALS_ID = 'lke-kubeconfig' // Replace with your credential ID
        SERVER_URL = 'https://<LKE_CLUSTER_ENDPOINT>' // Replace with your LKE cluster endpoint
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t my-app:latest .'
            }
        }

        stage('Push to Registry') {
            environment {
                DOCKER_IMAGE = 'my-app:latest'
                REGISTRY_URL = '<DOCKER_REGISTRY_URL>' // Replace with your registry URL
            }
            steps {
                sh 'docker login $REGISTRY_URL -u <USERNAME> -p <PASSWORD>'
                sh 'docker tag $DOCKER_IMAGE $REGISTRY_URL/my-app:latest'
                sh 'docker push $REGISTRY_URL/my-app:latest'
            }
        }

        stage('Deploy to LKE') {
            steps {
                withKubeConfig([credentialsId: KUBECONFIG_CREDENTIALS_ID, serverUrl: SERVER_URL]) {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
```

#### Best Practices:

- Use `withKubeConfig` to securely manage kubeconfig credentials.
- Define resource limits in your Kubernetes manifests for efficient cluster usage.
- Use multi-stage builds in Dockerfiles to optimize image size.

---

## Useful Commands

### Verify Cluster Connection

```bash
kubectl cluster-info
```

### Check Jenkins Logs

```bash
sudo docker logs jenkins
```

### Manage Kubernetes Resources

```bash
kubectl get all
kubectl describe pod <POD_NAME>
kubectl logs <POD_NAME>
```

---

## Additional Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Linode LKE Documentation](https://www.linode.com/docs/guides/kubernetes/)
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)

---

## Author

Happy Deploying!
