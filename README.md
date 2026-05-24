# Brain Tasks App - DevOps Deployment

## Project Overview

This project demonstrates the deployment of a frontend application using Docker, AWS ECR, Kubernetes (EKS), CodeBuild, and CodePipeline.

The application was containerized using Docker and deployed to an AWS EKS cluster using Kubernetes deployment and service configurations. CI/CD integration was also configured using AWS CodeBuild and CodePipeline.

---

## Technologies Used

* React / Vite Application
* Docker
* NGINX
* AWS ECR
* AWS EKS
* Kubernetes
* AWS CodeBuild
* AWS CodePipeline
* GitHub

---

## Application Setup

### Clone Repository

```bash
git clone https://github.com/Vennilavanguvi/Brain-Tasks-App.git
```

Move into project folder:

```bash
cd Brain-Tasks-App
```

---

## Docker Setup

### Build Docker Image

```bash
docker build -t brain-tasks-app .
```

### Run Docker Container

```bash
docker run -d -p 3000:3000 --name brain-container brain-tasks-app
```

Application runs on:

```bash
http://localhost:3000
```

---

## Dockerfile Used

```dockerfile
FROM nginx:alpine

COPY dist /usr/share/nginx/html

EXPOSE 3000

RUN sed -i 's/listen       80;/listen 3000;/g' /etc/nginx/conf.d/default.conf

CMD ["nginx", "-g", "daemon off;"]
```

---

## AWS ECR Setup

Created ECR repository:

```bash
aws ecr create-repository --repository-name brain-tasks-app --region ap-south-1
```

Docker login to ECR:

```bash
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <ECR_URI>
```

Push image to ECR:

```bash
docker tag brain-tasks-app:latest <ECR_URI>/brain-tasks-app:latest

docker push <ECR_URI>/brain-tasks-app:latest
```

---

## EKS Cluster Creation

EKS cluster was created using eksctl.

```bash
eksctl create cluster \
--name brain-tasks-cluster \
--region ap-south-1 \
--nodegroup-name brain-workers \
--node-type t3.small \
--nodes 2 \
--managed
```

Check nodes:

```bash
kubectl get nodes
```

---

## Kubernetes Deployment

### deployment.yaml

Created deployment with:

* 2 replicas
* container image from ECR
* container port 3000

Deploy command:

```bash
kubectl apply -f deployment.yaml
```

---

## Kubernetes Service

Service type used:

```yaml
LoadBalancer
```

Deploy service:

```bash
kubectl apply -f service.yaml
```

Check service:

```bash
kubectl get svc
```

The external LoadBalancer URL was used to access the application publicly.

---

## CI/CD Setup

### AWS CodeBuild

CodeBuild project was configured with:

* GitHub repository as source
* buildspec.yml for build instructions
* Docker privileged mode enabled

### buildspec.yml

Used to:

* login to ECR
* build Docker image
* tag image
* push image to ECR

---

## AWS CodePipeline

Pipeline stages:

1. Source - GitHub
2. Build - CodeBuild
3. Deploy - Push updated image to ECR

The pipeline automatically triggers when changes are pushed to GitHub.

---

## Monitoring

CloudWatch logs were used to monitor:

* CodeBuild logs
* Build errors
* Deployment status

---

## Output

The application was successfully:

* Dockerized
* pushed to AWS ECR
* deployed on AWS EKS
* exposed using Kubernetes LoadBalancer
* integrated with CI/CD pipeline

---

## Screenshots Included

* Docker container running
* ECR repository
* EKS cluster
* Kubernetes pods and services
* LoadBalancer output
* CodeBuild success
* CodePipeline success
* Application running in browser
