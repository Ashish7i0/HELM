# Kubernetes Deployment for Learner Report MERN Application

This repository contains Kubernetes deployment files and HELM charts for the Learner Report MERN stack application.

## Prerequisites
- Kubernetes cluster
- Helm
- Docker
- Jenkins (for CI/CD pipeline)
- MongoDB (for database)

## Directory Structure
```
├── k8s/                         # Kubernetes manifests
│   ├── frontend/                # Frontend Kubernetes files
│   ├── backend/                 # Backend Kubernetes files
│   └── mongodb/                 # MongoDB Kubernetes files
├── helm-chart/                  # HELM chart
│   └── learner-report/          
│       ├── templates/           # Kubernetes templates
│       ├── Chart.yaml           # Chart metadata
│       └── values.yaml          # Configuration values
├── Jenkinsfile                  # CI/CD pipeline script
└── learner-report-cs/           # Application source code
    ├── frontend/                # Frontend source
    └── backend/                 # Backend source
```

## Deployment Methods

### 1. Infrastructure Setup with Terraform
```bash
# Navigate to the infra directory
cd infra

# Initialize Terraform
terraform init

# Apply the Terraform configuration to create the EC2 instance
terraform apply -auto-approve
```

### 2. Using Kubernetes Manifests Directly
```bash
# SSH into the EC2 instance (replace <EC2_IP> with the instance IP from Terraform output)
ssh -i ~/.ssh/MERN-K8s-Key.pem ec2-user@<EC2_IP>

# Apply Kubernetes manifests
kubectl apply -f k8s/mongodb/
kubectl apply -f k8s/backend/
kubectl apply -f k8s/frontend/
```

### 3. Using HELM Chart (Recommended)
```bash
# SSH into the EC2 instance (replace <EC2_IP> with the instance IP from Terraform output)
ssh -i ~/.ssh/MERN-K8s-Key.pem ec2-user@<EC2_IP>

# Install chart
helm install learner-report helm-chart/learner-report

# Upgrade deployment if already installed
helm upgrade learner-report helm-chart/learner-report
```

## CI/CD Pipeline
The Jenkinsfile provides a pipeline to:
1. Build Docker images for frontend and backend
2. Push images to a Docker registry
3. Deploy to Kubernetes using Helm

### Setup Instructions:
1. Replace placeholders in Jenkinsfile with your Docker registry and credentials
2. Create Jenkins credentials for Docker Hub and kubeconfig
3. Run the pipeline

## Accessing the Application
After deployment, get the frontend service external IP:
```bash
kubectl get svc frontend-service
```

Access the application at `http://<EXTERNAL-IP>`

## Configuration
Modify values in `helm-chart/learner-report/values.yaml` for:
- Replica counts
- Docker image tags
- Resource limits
- Database name
