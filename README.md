# 🚀 DevOps Project: Book My Show Clone App Deployment

Welcome to the Book My Show Clone App Deployment project! This project demonstrates a robust DevOps pipeline for deploying a Book My Show clone application using modern tools and practices, following a DevSecOps approach. It includes containerization with Docker, orchestration with Amazon EKS (Elastic Kubernetes Service), CI/CD with Jenkins, infrastructure-as-code (IaC) with Terraform, and monitoring with Prometheus and Grafana.

## 🛠️ Tools & Services Used

| Category            | Tools/Services                              |
|---------------------|---------------------------------------------|
| Version Control     | GitHub                                      |
| CI/CD               | Jenkins                                     |
| Infrastructure-as-Code (IaC) | Terraform                           |
| Containerization    | Docker                                      |
| Orchestration       | Kubernetes, Amazon EKS, Helm                 |
| Monitoring          | Prometheus, Grafana                         |
| Security            | SonarQube, Trivy, OWASP                     |
| Cloud               | AWS (EKS, EC2), AWS CLI                     |
| Scripting           | Bash, YAML                                  |

## 🚦 Project Stages

### Part I: Docker Deployment

#### Step 1: Basic Setup
- **Launch VM**: Ubuntu 24.04, t2.large, 28 GB, named `BMS-Server`.
- **Security Group Configuration**:
  - SMTP: TCP 25
  - Custom TCP: 3000-10000 (Node.js, Grafana, Jenkins, etc.)
  - HTTP: TCP 80
  - HTTPS: TCP 443
  - SSH: TCP 22
  - Custom TCP: 6443 (Kubernetes API)
  - SMTPS: TCP 465
  - Custom TCP: 30000-32767 (Kubernetes NodePort)
- **IAM User Creation**:
  - Attached policies: `AmazonEC2FullAccess`, `AmazonEKS_CNI_Policy`, `AmazonEKSClusterPolicy`, `AmazonEKSWorkerNodePolicy`, `AWSCloudFormationFullAccess`, `IAMFullAccess`.
  - Inline policy: `eks:*` actions.
  - Generated Access and Secret Keys.
- **EKS Cluster Creation**:
  1. Installed AWS CLI, `kubectl`, and `eksctl`.
  2. Created EKS cluster with `eksctl`:
     - Command: `eksctl create cluster --name=aman-eks --region=us-east-1 --zones=us-east-1a,us-east-1b --version=1.30 --without-nodegroup`.
     - Associated IAM OIDC provider: `eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster kastro-eks --approve`.
     - Created node group: `eksctl create nodegroup --cluster=amank-eks --region=us-east-1 --name=node2 --node-type=t3.medium --nodes=3 --nodes-min=2 --nodes-max=4 --node-volume-size=20 --ssh-access --ssh-public-key=BMS-key --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access`.

#### Step 2: Tools Installation
- **Jenkins Setup**:
  - Installed Java 17 and Jenkins using scripts (`Jenkins.sh`).
  - Opened port 8080, accessed Jenkins, and configured plugins (e.g., SonarQube Scanner, Docker, Kubernetes).
- **Docker Setup**:
  - Installed Docker using `docker.sh`, pulled `hello-world` image, and logged into DockerHub.
- **Trivy Setup**:
  - Installed Trivy using `trivy.sh` for filesystem and container vulnerability scanning.
- **SonarQube Setup**:
  - Ran `docker run -d --name sonar -p 9000:9000 sonarqube:lts-community` and configured with default credentials (`admin`).

#### Step 3: Access Jenkins Dashboard
- Installed plugins: Eclipse Temurin Installer, SonarQube Scanner, NodeJS, Docker-related plugins, OWASP Dependency Check, Pipeline Stage View, Email Extension, Kubernetes plugins.
- Configured SonarQube token (`squ_69eb05b41575c699579c6ced901eaafae66d63a2`) and credentials.

#### Step 4: Email Integration
- Configured Gmail app password in Jenkins for SMTP notifications (smtp.gmail.com:465, SSL enabled).
- Set up email triggers for build success/failure.

#### Step 5: Create Pipeline Job
- **Pipeline Script (Without K8S)**:
  - Cleaned workspace, checked out GitHub code, ran SonarQube analysis, installed dependencies, performed Trivy FS scan, built and pushed Docker image (`Amank438/bms:latest`), and deployed to a container on port 3000.
  - Sent email notifications with logs and `trivyfs.txt`.
- **Pipeline Script (With K8S)**:
  - Added EKS deployment stage: Configured `kubectl` with `aws eks update-kubeconfig`, applied `deployment.yml` and `service.yml`, and verified pods/services.

### Part II: Kubernetes Deployment & Monitoring

#### Step 6: Monitoring the Application
- **Prometheus Setup** (Monitoring Server, Ubuntu 22.04, t2.medium):
  - Created `prometheus` system user.
  - Downloaded and installed Prometheus 2.47.1, configured systemd service (`/etc/systemd/system/prometheus.service`), and opened port 9090.
  - Installed Node Exporter 1.6.1, configured systemd service (`/etc/systemd/system/node_exporter.service`), and opened port 9100.
  - Updated `prometheus.yml` with jobs for `node_exporter` (`<MonitoringVMip>:9100`) and `jenkins` (`<your-jenkins-ip>:<your-jenkins-port>/prometheus`).
- **Grafana Setup** (Monitoring Server):
  - Installed Grafana, enabled service, and accessed via port 3000.
  - Added data source (Prometheus) and dashboards (Node Exporter: ID 1860, Jenkins: ID 9964).

## 📂 Code Repository

Explore the code and contribute to the project:
- [GitHub Repository](https://github.com/codexaman9901/DEPLOYMENT-OF-BOOK-MY-SHOW-APP)



## 🚀 How to Run

1. **Prerequisites**:
   - AWS account with IAM user and Access/Secret Keys.
   - EC2 instance (`BMS-Server` and `Monitoring Server`) with Ubuntu 22.04/24.04.
   - DockerHub account and Jenkins setup.

2. **Setup**:
   - Launch VMs, configure security groups, and install tools (AWS CLI, `kubectl`, `eksctl`, Jenkins, Docker, Trivy, SonarQube).
   - Create EKS cluster and node group using `eksctl`.
   - Configure Jenkins pipeline with the provided script.

3. **Deployment**:
   - Run the Jenkins pipeline (select `apply` for creation, `destroy` to clean up).
   - Access the app via `BMS-Server` public IP on port 3000 or EKS service endpoint.

4. **Monitoring**:
   - Access Prometheus at `<MonitoringVMip>:9090` and Grafana at `<MonitoringVMip>:3000`.

5. **Cleanup**:
   - Terminate EC2 instances and delete EKS resources after testing.




