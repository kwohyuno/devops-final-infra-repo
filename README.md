# DevOps Final Infrastructure Repository

This repository contains the infrastructure and deployment configurations for a multi-tier web application with blue-green deployment strategy using Kubernetes and AWS services.

## 🏗️ Architecture Overview

The application consists of three main components:
- **Frontend**: React/Node.js application (port 80)
- **Backend1**: Spring Boot application for post functionality (port 8080)
- **Backend2**: Spring Boot application for signup functionality (port 8081)

### Infrastructure Stack
- **Container Orchestration**: Kubernetes (EKS)
- **Container Registry**: AWS ECR
- **Database**: Amazon RDS (MySQL)
- **CI/CD**: GitHub Actions
- **Deployment Strategy**: Blue-Green Deployment

## 📁 Repository Structure

```
devops-final-infra-repo/
├── .github/
│   └── workflows/
│       ├── deploy-to-qa.yml          # QA environment deployment
│       ├── deploy-to-uat-blue.yml    # UAT blue deployment
│       ├── deploy-to-uat-green.yaml  # UAT green deployment
│       └── nightly-build.yml         # Nightly build and testing
├── k8s/
│   ├── prod/                         # Production environment manifests
│   │   ├── frontend-deployment-blue.yaml
│   │   ├── frontend-deployment-green.yaml
│   │   ├── backend1-deployment-blue.yaml
│   │   ├── backend1-deployment-green.yaml
│   │   ├── backend2-deployment-blue.yaml
│   │   ├── backend2-deployment-green.yaml
│   │   └── [service files...]
│   └── uat/                          # UAT environment manifests
│       ├── frontend-deployment-blue.yaml
│       ├── frontend-deployment-green.yaml
│       ├── backend1-deployment-blue.yaml
│       ├── backend1-deployment-green.yaml
│       ├── backend2-deployment-blue.yaml
│       ├── backend2-deployment-green.yaml
│       └── [service files...]
└── argocd.log                        # ArgoCD deployment logs
```

## 🚀 Deployment Environments

### Production Environment
- **Cluster**: EKS cluster (`ekscluster`)
- **Region**: us-east-1
- **Strategy**: Blue-Green deployment with separate blue and green versions
- **Components**:
  - Frontend 
  - Backend1 (Post) 
  - Backend2 (Signup 

### UAT Environment
- **Cluster**: EKS cluster (`ekscluster`)
- **Region**: us-east-1
- **Strategy**: Blue-Green deployment for testing
- **Components**: Same as production with UAT-specific configurations

### QA Environment
- **Infrastructure**: Temporary EC2 instances for testing
- **Strategy**: Automated nightly builds and testing
- **Components**: Docker containers running on EC2

## 🔄 CI/CD Pipeline

### GitHub Actions Workflows

#### 1. Nightly Build (`nightly-build.yml`)
- **Trigger**: Manual (workflow_dispatch)
- **Purpose**: Automated testing on temporary EC2 instances
- **Process**:
  - Launches temporary EC2 instance
  - Installs Docker and dependencies
  - Pulls latest images from ECR
  - Runs application tests
  - Terminates EC2 instance after testing

#### 2. Deploy to QA (`deploy-to-qa.yml`)
- **Trigger**: Daily at midnight (cron: "0 0 * * *") or manual
- **Purpose**: Automated QA environment deployment
- **Process**:
  - Similar to nightly build but with additional deployment steps
  - Runs comprehensive testing on QA environment

#### 3. Deploy to UAT - Blue (`deploy-to-uat-blue.yml`)
- **Trigger**: Manual (workflow_dispatch)
- **Purpose**: Deploy blue version to UAT environment
- **Process**:
  - Applies blue deployment manifests
  - Updates ingress to point to blue version
  - Waits for rollout completion

#### 4. Deploy to UAT - Green (`deploy-to-uat-green.yaml`)
- **Trigger**: Manual (workflow_dispatch)
- **Purpose**: Deploy green version to UAT environment
- **Process**:
  - Applies green deployment manifests
  - Updates ingress to point to green version
  - Waits for rollout completion

## 🗄️ Database Configuration

### RDS MySQL Database
- **Endpoint**: `database-1.cyxm60si8u5p.us-east-1.rds.amazonaws.com`
- **Database**: `jake_dbschema`
- **Port**: 3306
- **Credentials**: Configured via environment variables

## 🔧 Prerequisites

### Required Secrets
The following GitHub secrets must be configured:
- `AWS_ACCESS_KEY_ID`: AWS access key
- `AWS_SECRET_ACCESS_KEY`: AWS secret key
- `AWS_SESSION_TOKEN`: AWS session token
- `AWS_REGION`: AWS region (us-east-1)
- `EC2_SSH_PRIVATE_KEY`: SSH private key for EC2 access

### Required AWS Resources
- EKS cluster named `ekscluster`
- ECR repository for container images
- RDS MySQL instance
- EC2 key pair named `devops`
- Security group with appropriate rules
- IAM instance profile for EC2

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/kwohyuno/devops-final-infra-repo.git
cd devops-final-infra-repo
```

### 2. Configure AWS Credentials
Ensure your AWS credentials are properly configured with the required permissions.

### 3. Deploy to UAT (Blue)
1. Go to GitHub Actions tab
2. Select "Deploy to UAT - Blue" workflow
3. Click "Run workflow"

### 4. Deploy to UAT (Green)
1. Go to GitHub Actions tab
2. Select "Deploy to UAT - Green" workflow
3. Click "Run workflow"

### 5. Deploy to Production
Apply the production manifests to your EKS cluster:
```bash
kubectl apply -f k8s/prod/
```

## 🔍 Monitoring and Troubleshooting

### Check Pod Status
```bash
kubectl get pods -n <namespace>
kubectl describe pod <pod-name>
```

### Check Services
```bash
kubectl get services
kubectl describe service <service-name>
```

### Check Logs
```bash
kubectl logs <pod-name>
kubectl logs -f <pod-name>  # Follow logs
```

### ArgoCD Issues
If you encounter ArgoCD port conflicts (as shown in `argocd.log`), ensure port 8080 is not already in use:
```bash
lsof -i :8080
```

## 📝 Notes

- The application uses blue-green deployment strategy for zero-downtime deployments
- All container images are stored in AWS ECR
- Database credentials are hardcoded in deployment manifests (consider using Kubernetes secrets for production)
- The nightly build process automatically cleans up temporary EC2 instances
- UAT environment uses the same EKS cluster as production but with different namespaces

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is part of a DevOps final project and is intended for educational purposes. 
