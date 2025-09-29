# Go Web Application on AWS EKS

A simple Go web application deployed on AWS Elastic Kubernetes Service (EKS) using Fargate, Helm, and ArgoCD for automated deployments.
This project demonstrates how to containerize a Go application, deploy it to a managed Kubernetes cluster, and manage deployments with GitOps.

# Overview

# Language: Go

# Deployment: AWS EKS with Fargate

# Package Manager: Helm

# Deployment Tool: ArgoCD (GitOps approach)

# Features:

Simple HTTP server using net/http

Containerized for Kubernetes

Easy image updates via Helm

Automated deployment and sync via ArgoCD

# Prerequisites

# Install the following tools on your machine:
```bash

# AWS CLI
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

# kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/$(uname | tr '[:upper:]' '[:lower:]')/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# ArgoCD CLI
curl -sSL -o argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x argocd
sudo mv argocd /usr/local/bin/

```

# Create EKS Cluster with Fargate

```bash

eksctl create cluster \
  --name demo-cluster \
  --region us-east-1 \
  --fargate

```


# Check cluster nodes:

```bash

kubectl get nodes

```

 Build and Push Docker Image
# Navigate to your Go app directory

```bash

cd go-web-app

```

# Build Docker image

```bash

docker build -t <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/go-web-app:latest .

```

# Login to AWS ECR

```bash

aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

```

# Push Docker image to ECR

```bash

docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/go-web-app:latest

```

 Deploy using Helm
 
# Install Helm chart (replace <TAG> with your image tag)

```bash
helm upgrade --install go-web-app ./helm-chart \
  --set image.repository=<AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/go-web-app \
  --set image.tag=latest

```bash


Verify deployment:

```bash

kubectl get pods
kubectl get svc

```

 Deploy using ArgoCD
 
# Login to ArgoCD server (example assumes port-forward)

```bash

kubectl port-forward svc/argocd-server -n argocd 8080:443
argocd login localhost:8080 --username admin --password <ARGOCD_PASSWORD> --insecure

```

# Add GitHub repository

```bash

argocd repo add https://github.com/your-username/your-repo.git

```

# Create ArgoCD application

```bash

argocd app create go-web-app \
  --repo https://github.com/your-username/your-repo.git \
  --path helm-chart \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default

```

# Sync the application

```bash

argocd app sync go-web-app
```

Access the Application

```bash

kubectl get svc go-web-app

```

## Looks like this

[![Website](static/images/golang-website.png)](http://go-web-app.local/home)



