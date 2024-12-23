# Demo Project: Complete CI/CD Pipeline with EKS and Private DockerHub Registry

## Overview
This project demonstrates a complete CI/CD pipeline for a Java Maven application. The pipeline automates the process of building, containerizing, and deploying an application to an AWS EKS cluster using a private DockerHub registry. It includes steps for versioning, artifact creation, Docker image management, deployment to Kubernetes, and version tracking in Git.

## Technologies Used
- **Kubernetes**
- **Jenkins**
- **AWS EKS**
- **Docker Hub** (Private Registry)
- **Java**
- **Maven**
- **Linux**
- **Docker**
- **Git**

---

## Prerequisites

1. **AWS Account:** Access to an AWS account with permissions to manage EKS and IAM roles.
2. **Jenkins Server:** A running Jenkins instance with Docker and required plugins.
3. **GitHub or GitLab Repository:** Repository for managing source code and CI/CD configurations.
4. **DockerHub Account:** Private registry with necessary credentials.
5. **kubectl:** Kubernetes command-line tool installed and configured.
6. **Kubernetes Cluster:** AWS EKS cluster set up and accessible.

---

## Project Steps

### 1. CI/CD Pipeline Configuration

#### Jenkinsfile
The following Jenkinsfile defines the complete CI/CD pipeline:

```groovy
#!/usr/bin/env groovy

pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        DOCKER_REPO_SERVER = '<Your repository server>'
        DOCKER_REPO = "${DOCKER_REPO_SERVER}/java-maven-app"
    }
    stages {
        stage('Increment Version') {
            steps {
                script {
                    echo 'Incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion}.\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }

        stage('Build App') {
            steps {
                script {
                    echo 'Building the application...'
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building and pushing Docker image...'
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t ${DOCKER_REPO}:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin ${DOCKER_REPO_SERVER}'
                        sh "docker push ${DOCKER_REPO}:${IMAGE_NAME}"
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            environment {
                AWS_ACCESS_KEY_ID = credentials('jenkins-aws-access-key-id')
                AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
                APP_NAME = 'java-maven-app'
            }
            steps {
                script {
                    echo 'Deploying to EKS...'
                    sh 'envsubst < kubernetes/deployment.yaml | kubectl apply -f -'
                    sh 'envsubst < kubernetes/service.yaml | kubectl apply -f -'
                }
            }
        }

        stage('Commit Version Update') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "git remote set-url origin https://${USER}:${PASS}@<your-github-repository>"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:main'
                    }
                }
            }
        }
    }
}
```

### Explanation of Jenkinsfile:

1. **Increment Version:**
   - Automatically increments the application version using Maven's build-helper plugin.

2. **Build App:**
   - Compiles and packages the Java Maven application.

3. **Build Docker Image:**
   - Builds the Docker image for the application.
   - Logs into DockerHub using credentials.
   - Pushes the image to a private DockerHub repository.

4. **Deploy to EKS:**
   - Substitutes variables in Kubernetes manifest files and applies them to the EKS cluster.

5. **Commit Version Update:**
   - Updates the repository with the new version number and pushes changes to GitHub.

---

### 2. Kubernetes Manifest Files

#### Deployment Configuration (`deployment.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: $APP_NAME
  labels:
    app: $APP_NAME
spec:
  replicas: 2
  selector:
    matchLabels:
      app: $APP_NAME
  template:
    metadata:
      labels:
        app: $APP_NAME
    spec:
      imagePullSecrets:
        - name: aws-registry-key
      containers:
        - name: $APP_NAME
          image: $DOCKER_REPO:$IMAGE_NAME
          imagePullPolicy: Always
          ports:
            - containerPort: 8080
```

#### Service Configuration (`service.yaml`):

```yaml
apiVersion: v1
kind: Service
metadata:
  name: $APP_NAME
spec:
  selector:
    app: $APP_NAME
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

### Best Practices for Kubernetes Configuration:
- Use `imagePullSecrets` to authenticate private registries.
- Always specify resource requests and limits in the deployment.
- Use descriptive labels for easy management.

---

### GitHub Integration
To integrate Jenkins with GitHub:

1. **Add GitHub Credentials:**
   - Navigate to Jenkins Dashboard > Manage Jenkins > Manage Credentials.
   - Add GitHub credentials as a username/password pair.

2. **Update Jenkinsfile:**
   - Replace `<your-github-repository>` with the URL of your GitHub repository.

3. **Webhook Configuration:**
   - Set up a webhook in your GitHub repository to trigger Jenkins builds on push events.

---

## Additional Resources
- [Jenkins Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [AWS EKS Guide](https://docs.aws.amazon.com/eks/)
- [DockerHub Documentation](https://docs.docker.com/docker-hub/)
- [GitHub Webhooks](https://docs.github.com/en/developers/webhooks-and-events/webhooks/creating-webhooks)

---

Happy Deploying!
