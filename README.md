<img src="https://github.com/Walidbadry/Localhost_Docker-Is-Our-LIfe/blob/main/Capture.PNG" alt="Pipeline Diagram" width="1000" height = "500">

## Overview
This project demonstrates a complete CI/CD DevOps pipeline running on a local environment. It integrates multiple DevOps tools to automate the software development lifecycle, ensuring continuous integration, testing, security scans, and deployment.

## Tech Stack & Tools Used
- **Version Control:** GitHub
- **CI/CD Automation:** Jenkins
- **Build Tool:** Maven
- **Code Quality Analysis:** SonarQube
- **Security Scanning:** Trivy, KubeHunter
- **Artifact Repository:** Nexus Repository
- **Containerization:** Docker
- **Orchestration:** Kubernetes
- **Monitoring & Logging:** Prometheus, Grafana
- **Notification System:** Gmail

## Pipeline Workflow
1. **Developer commits code** to the GitHub repository.
2. **Jenkins triggers the pipeline** upon a code push.
3. **Compilation and Testing:**
   - Code is compiled using Maven.
   - Unit tests are executed.
4. **Code Quality & Security Checks:**
   - Code is analyzed using SonarQube.
   - File system scanning is performed.
   - Trivy scans Docker images for vulnerabilities.
   - KubeHunter performs a security scan on Kubernetes.
5. **Build and Push Artifacts:**
   - Build artifacts are pushed to Nexus Repository.
   - A Docker image is built and tagged.
   - The Docker image is pushed to the local Docker registry.
6. **Deployment to Kubernetes:**
   - Docker image is deployed to the Kubernetes cluster.
   - The deployment is verified.
7. **Monitoring & Notifications:**
   - Prometheus and Grafana collect monitoring data.
   - Email notifications are sent via Gmail for pipeline updates.
   
## Prerequisites
Ensure the following tools are installed on your localhost:
- Docker
- Kubernetes (Minikube or kind for local setup)
- Jenkins
- SonarQube
- Nexus Repository
- Trivy
- Prometheus & Grafana

## Setup Instructions
1. **Clone the Repository:**
   ```sh
   git clone [<repo-url>](https://github.com/Walidbadry/Localhost_Docker-Is-Our-LIfe.git)
   cd /Localhost_Docker-Is-Our-LIfe
   ```
2. **Start Jenkins, SonarQube, and Nexus:**
   ```sh
      docker run -d --name jenkins --network devops_network -p 8080:8080 -v jenkins_data:/var/jenkins_home jenkins/jenkins:lts
      
      docker run -d --name sonarqube --network devops_network -p 9000:9000 -v sonarqube_data:/opt/sonarqube sonarqube:lts
      
      docker run -d --name nexus --network devops_network -p 8081:8081 -v nexus_data:/nexus-data sonatype/nexus3
      
   ```
3. **Configure Jenkins:**
   - Install required plugins (Maven, Docker, SonarQube Scanner, etc.).
   - Set up credentials for GitHub, Docker, and Nexus.
4. **Run the Pipeline:**
   - Access Jenkins at `http://localhost:8080`
   - Trigger the pipeline manually or configure webhook for GitHub.

## Deployment & Access
- Deployed application can be accessed via `http://localhost:<port>`.
- Kubernetes dashboard: `kubectl proxy` and access `http://localhost:8001/ui`.

## Future Improvements
- Automate infrastructure setup using Terraform.
- Implement Blue-Green or Canary deployment strategy.
- Enable RBAC for enhanced security.

## Contributors
- walid badry  (Project reproducer)


