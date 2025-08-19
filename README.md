# GitOps Demo: ArgoCD + Kubernetes + CI/CD

This repository demonstrates a complete GitOps workflow using ArgoCD for continuous deployment of a Java Spring Boot application on Kubernetes.

## 🏗️ Architecture Overview

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Developer     │    │   GitHub        │    │   Kubernetes    │
│   Push Code     │───▶│   Repository    │───▶│   Cluster       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │                         ▲
                              ▼                         │
                       ┌─────────────────┐              │
                       │  GitHub Actions │              │
                       │  CI/CD Pipeline │              │
                       └─────────────────┘              │
                              │                         │
                              ▼                         │
                       ┌─────────────────┐              │
                       │  Docker Hub     │              │
                       │  Image Registry │              │
                       └─────────────────┘              │
                              │                         │
                              ▼                         │
                       ┌─────────────────┐              │
                       │     ArgoCD      │──────────────┘
                       │  GitOps Engine  │
                       └─────────────────┘
```

## 📁 Repository Structure

```
├── .github/workflows/    # CI/CD pipeline configuration
│   └── ci.yml           # GitHub Actions workflow
├── argocd/              # ArgoCD application configuration
│   └── application.yaml # ArgoCD Application manifest
├── dev/                 # Kubernetes manifests for development
│   ├── deployment.yaml  # Application deployment
│   └── service.yaml     # Application service
├── src/                 # Java application source code
├── Dockerfile           # Container image definition
├── build.gradle         # Gradle build configuration
└── README.md           # This file
```

## 🚀 Quick Start

### Prerequisites

- Kubernetes cluster (minikube, kind, or cloud provider)
- ArgoCD installed in the cluster
- Docker Hub account (or container registry)
- kubectl configured

### 1. Deploy ArgoCD (if not already installed)

```bash
# Create argocd namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods to be ready
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

# Port forward to access UI
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address='0.0.0.0'
```

### 2. Deploy the Application via ArgoCD

```bash
# Apply the ArgoCD Application
kubectl apply -f argocd/application.yaml

# Check application status
kubectl get applications -n argocd
```

### 3. Access the Application

```bash
# Port forward to the application
kubectl port-forward svc/myapp-service -n myappmin 8080:8080

# Access at http://localhost:8080
```

## 🛠️ Development Workflow

### 1. Local Development

```bash
# Build the application
./gradlew build

# Run locally
./gradlew bootRun

# Build Docker image
docker build -t myapp:local .

# Run container locally
docker run -p 8080:8080 myapp:local
```

### 2. CI/CD Pipeline

The GitHub Actions workflow automatically:

1. **Build** - Compiles Java application with Gradle
2. **Test** - Runs unit tests
3. **Package** - Creates Docker image
4. **Push** - Uploads image to Docker Hub
5. **Deploy** - ArgoCD detects changes and deploys

### 3. GitOps Workflow

1. Developer pushes code changes
2. GitHub Actions builds and pushes new image
3. Update Kubernetes manifests with new image tag
4. ArgoCD automatically syncs changes to cluster
5. Application is updated with zero downtime

## 📋 Application Details

### Java Application
- **Framework**: Spring Boot
- **Java Version**: 8
- **Port**: 8080
- **Build Tool**: Gradle

### Container Configuration
- **Base Image**: openjdk:8-jre-alpine
- **Exposed Port**: 8080
- **Registry**: Docker Hub

### Kubernetes Resources
- **Deployment**: 2 replicas for high availability
- **Service**: ClusterIP for internal access
- **Namespace**: myappmin (auto-created)

## 🔧 Configuration

### ArgoCD Application

The ArgoCD Application is configured with:

- **Source**: This GitHub repository
- **Path**: `dev/` directory
- **Destination**: `myappmin` namespace
- **Sync Policy**: Automated with self-heal and prune

### Environment Variables

Configure these secrets in GitHub:

```bash
DOCKER_USERNAME=your-docker-username
DOCKER_PASSWORD=your-docker-password
```

## 🔍 Monitoring and Troubleshooting

### Check Application Status

```bash
# ArgoCD application status
kubectl get applications -n argocd

# Pod status
kubectl get pods -n myappmin

# Service status
kubectl get svc -n myappmin

# Logs
kubectl logs -f deployment/myapp -n myappmin
```

### Common Issues

1. **Image Pull Errors**: Check Docker Hub credentials
2. **Sync Issues**: Verify repository URL and path
3. **Network Issues**: Check service and ingress configuration

## 🔄 Update Application

### Method 1: GitOps (Recommended)

1. Update image tag in `dev/deployment.yaml`
2. Commit and push changes
3. ArgoCD automatically syncs

### Method 2: Manual Sync

```bash
# Via ArgoCD CLI
argocd app sync myapp-argo-application

# Via kubectl
kubectl patch application myapp-argo-application -n argocd --type='merge' -p='{"operation":{"initiatedBy":{"username":"admin"},"sync":{"syncStrategy":{}}}}'
```

## 📚 Learn More

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [GitOps Principles](https://www.gitops.tech/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Spring Boot Documentation](https://spring.io/projects/spring-boot)

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test locally
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🏷️ Tags

`gitops` `argocd` `kubernetes` `java` `spring-boot` `docker` `ci-cd` `github-actions`
    
