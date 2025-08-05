# Brain Tasks App – Production Deployment on AWS EKS

This project demonstrates deploying a React application to a production-ready state using Docker, AWS ECR, Kubernetes (EKS), CodeBuild, CodeDeploy, and CodePipeline. The application is containerized, deployed on AWS EKS, and exposed via a LoadBalancer.

---

## Table of Contents

- [Project Description](#project-description)
- [Architecture Overview](#architecture-overview)
- [Setup Instructions](#setup-instructions)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. Run Locally (Port 3000)](#2-run-locally-port-3000)
  - [3. Dockerize the Application](#3-dockerize-the-application)
  - [4. Push Docker Image to AWS ECR](#4-push-docker-image-to-aws-ecr)
  - [5. Deploy to AWS EKS](#5-deploy-to-aws-eks)
  - [6. CI/CD Pipeline](#6-cicd-pipeline)
- [Kubernetes LoadBalancer ARN](#kubernetes-loadbalancer-arn)
- [Pipeline Explanation](#pipeline-explanation)
- [Monitoring](#monitoring)
- [Screenshots](#screenshots)
- [Repository Structure](#repository-structure)
- [Version Control](#version-control)
- [License](#license)

---

## Project Description

This project automates the deployment of a React application using the following AWS services:

- **Docker**: Containerizes the application.
- **ECR**: Stores Docker images.
- **EKS**: Runs the application in a Kubernetes cluster.
- **CodeBuild**: Builds and pushes Docker images.
- **CodeDeploy**: Deploys the application to EKS.
- **CodePipeline**: Automates the CI/CD process.
- **CloudWatch**: Monitors logs and deployments.

---

## Architecture Overview

1. **Source**: Code is pushed to GitHub.
2. **Build**: CodeBuild builds and pushes Docker images to ECR.
3. **Deploy**: CodeDeploy deploys the image to EKS using Kubernetes manifests.
4. **Service**: The app is exposed via a LoadBalancer service.

---

## Setup Instructions

### 1. Clone the Repository

```sh
git clone <your-repo-url>
cd Brain-Tasks-App
```

### 2. Run Locally (Port 3000)

```sh
npm install -g serve
serve -s build -l 3000
# Application runs on http://localhost:3000
```

### 3. Dockerize the Application

Build and run the Docker image locally:

```sh
docker build -t brain-tasks-app .
docker run -p 3000:80 brain-tasks-app
# Access at http://localhost:3000
```

### 4. Push Docker Image to AWS ECR

1. **Create an ECR repository** (if not already created):

   ```sh
   aws ecr create-repository --repository-name brain-tasks-app
   ```

2. **Authenticate Docker to your ECR registry:**

   ```sh
   aws ecr get-login-password --region <your-region> | docker login --username AWS --password-stdin <your-account-id>.dkr.ecr.<your-region>.amazonaws.com
   ```

3. **Tag your image:**

   ```sh
   docker tag brain-tasks-app:latest <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/brain-tasks-app:latest
   ```

4. **Push the image:**

   ```sh
   docker push <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/brain-tasks-app:latest
   ```

### 5. Deploy to AWS EKS

1. **Update your Kubernetes manifests** (`deployment.yaml`, `service.yaml`) with your ECR image URI.
2. **Apply the manifests:**

   ```sh
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

3. **Check deployment status:**

   ```sh
   kubectl get pods
   kubectl get svc
   ```

### 6. CI/CD Pipeline

- **CodeBuild**: Builds and pushes Docker image (see `buildspec.yml`)
- **CodeDeploy**: Deploys to EKS (see `appspec.yml`)
- **CodePipeline**: Automates the process (Source: GitHub, Build: CodeBuild, Deploy: CodeDeploy)

---

## Kubernetes LoadBalancer ARN

> Replace with your actual LoadBalancer ARN after deployment.

```
arn:aws:elasticloadbalancing:<your-region>:<your-account-id>:loadbalancer/app/brain-tasks-service/xxxxxxxxxxxxxxxx

arn:aws:elasticloadbalancing:ap-south-1:346367214911:loadbalancer/app/aa18f9554c21a4fefb0206db5c7c4d96/50dc6a7f2c0c9188

```

---

## Pipeline Explanation

- **buildspec.yml**: Defines build steps for CodeBuild (login to ECR, build/tag/push Docker image, output artifacts).
- **appspec.yml**: Used by CodeDeploy to specify EKS service and container details.
- **deployment.yaml**: Kubernetes deployment manifest for the app.
- **service.yaml**: Exposes the app as a LoadBalancer.
- **nginx.conf**: Custom Nginx config for serving the React app.

---

## Monitoring

- **CloudWatch Logs**: Monitors build, deploy, and application logs.

---

## Screenshots

> Add screenshots of:
> - Run Locally
![alt text](01.png)
![alt text](02.png)
> - AWS ECR repository
> - EKS cluster and workloads
> - CodeBuild and CodePipeline
> - LoadBalancer in AWS Console

---

## Repository Structure

```
Brain-Tasks-App/
├── dist                # React build artifacts
├── appspec.yml         # CodeDeploy spec for EKS
├── buildspec.yml       # CodeBuild build instructions
├── deployment.yaml     # Kubernetes deployment manifest
├── dockerfile          # Dockerfile for building the app image
├── nginx.conf          # Nginx configuration for serving the app
├── service.yaml        # Kubernetes service manifest
```

---

## Version Control

- Code is managed via Git and pushed to GitHub using standard CLI commands:

```sh
git add .
git commit -m "<your commit message"
git push origin main
```

---
