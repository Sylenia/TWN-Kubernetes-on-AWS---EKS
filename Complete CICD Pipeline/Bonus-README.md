# Best Practices and Command Section for Modern DevOps Practices

## Best Practices

### General DevOps Best Practices
1. **Version Control Everything:**
   - Use Git to version control your code, configuration files, and infrastructure-as-code templates.

2. **Infrastructure as Code (IaC):**
   - Use tools like Terraform or AWS CloudFormation to define your infrastructure in code for consistency and repeatability.

3. **Automation Everywhere:**
   - Automate builds, tests, deployments, and monitoring.

4. **Continuous Integration/Continuous Deployment (CI/CD):**
   - Implement pipelines for automated builds, testing, and deployments.

5. **Fail Fast, Recover Quickly:**
   - Use automated testing to catch issues early. Design rollback strategies for failed deployments.

6. **Environment Consistency:**
   - Keep development, staging, and production environments consistent using containerization (e.g., Docker).

7. **Secrets Management:**
   - Use tools like AWS Secrets Manager or HashiCorp Vault to manage sensitive credentials securely.

8. **Monitoring and Logging:**
   - Implement comprehensive logging and monitoring solutions (e.g., Prometheus, Grafana, ELK Stack).

### Security Best Practices
1. **Principle of Least Privilege:**
   - Limit permissions for users and services to only what is necessary.

2. **Encrypt Everything:**
   - Use HTTPS for communication and encrypt sensitive data at rest and in transit.

3. **Multi-Factor Authentication (MFA):**
   - Enforce MFA for accessing critical resources.

4. **Regular Audits:**
   - Conduct regular audits of infrastructure and configurations.

5. **Container Security:**
   - Scan container images for vulnerabilities using tools like Trivy or Aqua Security.

6. **Secure Your CI/CD Pipelines:**
   - Limit access to CI/CD systems and avoid hardcoding credentials in pipelines.

7. **Patch Management:**
   - Regularly update systems, libraries, and dependencies to patch vulnerabilities.

---

## Command Section

### Git Commands
- **Clone a Repository:**
  ```bash
  git clone <repository_url>
  ```
  *Clones the repository to your local machine.*

- **Create a Branch:**
  ```bash
  git checkout -b <branch_name>
  ```
  *Creates a new branch for isolated development.*

- **Push Changes:**
  ```bash
  git push origin <branch_name>
  ```
  *Pushes your local changes to the remote branch.*

### Docker Commands
- **Build an Image:**
  ```bash
  docker build -t <image_name>:<tag> .
  ```
  *Builds a Docker image from the Dockerfile in the current directory.*

- **Run a Container:**
  ```bash
  docker run -d -p 8080:80 <image_name>:<tag>
  ```
  *Runs the container in detached mode, mapping port 8080 to 80.*

- **Push to Registry:**
  ```bash
  docker push <repository>/<image_name>:<tag>
  ```
  *Pushes the Docker image to a container registry.*

### Kubernetes Commands
- **Get Cluster Info:**
  ```bash
  kubectl cluster-info
  ```
  *Displays cluster information.*

- **Apply Configuration:**
  ```bash
  kubectl apply -f <manifest_file>
  ```
  *Applies a Kubernetes manifest file.*

- **Get Pods:**
  ```bash
  kubectl get pods
  ```
  *Lists all running pods.*

- **Check Logs:**
  ```bash
  kubectl logs <pod_name>
  ```
  *Displays logs for a specific pod.*

### AWS CLI Commands
- **Authenticate Docker with ECR:**
  ```bash
  aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <ecr_uri>
  ```
  *Authenticates Docker with the AWS Elastic Container Registry.*

- **Create an ECR Repository:**
  ```bash
  aws ecr create-repository --repository-name <repository_name>
  ```
  *Creates a new private ECR repository.*

### Jenkins Pipeline Commands
- **Restart Jenkins:**
  ```bash
  sudo systemctl restart jenkins
  ```
  *Restarts the Jenkins service.*

- **Check Logs:**
  ```bash
  sudo tail -f /var/log/jenkins/jenkins.log
  ```
  *Displays the Jenkins logs in real-time.*

- **Trigger a Build Manually:**
  ```bash
  curl -X POST http://<jenkins_url>/job/<job_name>/build --user <username>:<token>
  ```
  *Triggers a Jenkins job build via the command line.*

### Maven Commands
- **Clean and Package:**
  ```bash
  mvn clean package
  ```
  *Cleans and packages the Maven project.*

- **Increment Version:**
  ```bash
  mvn build-helper:parse-version versions:set -DnewVersion=<new_version> versions:commit
  ```
  *Updates the Maven project version.*

### Monitoring and Logging Commands
- **Check CPU Usage:**
  ```bash
  top
  ```
  *Monitors CPU usage in real time.*

- **Check Disk Space:**
  ```bash
  df -h
  ```
  *Displays disk usage in a human-readable format.*

- **View System Logs:**
  ```bash
  journalctl -xe
  ```
  *Displays detailed system logs.*

---

## Sum
This README provides best practices for building secure and efficient CI/CD pipelines and a comprehensive list of commonly used commands. These commands and practices are essential for DevOps practitioners to create, manage, and maintain reliable pipelines and infrastructure effectively.
