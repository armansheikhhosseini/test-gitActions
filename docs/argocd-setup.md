# ArgoCD Setup Guide

This guide walks you through setting up ArgoCD in your Kubernetes cluster and deploying applications using GitOps.

## Prerequisites

- Kubernetes cluster (v1.16+)
- kubectl configured
- Cluster admin permissions

## Installation

### 1. Install ArgoCD

```bash
# Create argocd namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for all pods to be ready
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```

### 2. Access ArgoCD UI

#### Method 1: Port Forward (Development)

```bash
# Port forward to ArgoCD server
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address='0.0.0.0'

# Access at https://localhost:8080
```

#### Method 2: LoadBalancer (Production)

```bash
# Change service type to LoadBalancer
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

# Get external IP
kubectl get svc argocd-server -n argocd
```

#### Method 3: NodePort

```bash
# Change service type to NodePort
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'

# Get node port
kubectl get svc argocd-server -n argocd
```

### 3. Login to ArgoCD

#### Get Admin Password

```bash
# Get initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

#### Login Credentials

- **Username**: `admin`
- **Password**: Output from the command above

### 4. Install ArgoCD CLI (Optional)

#### macOS

```bash
brew install argocd
```

#### Linux

```bash
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

#### Windows

```powershell
# Using Chocolatey
choco install argocd-cli

# Or download from GitHub releases
# https://github.com/argoproj/argo-cd/releases
```

#### Login via CLI

```bash
# Login to ArgoCD
argocd login localhost:8080

# List applications
argocd app list
```

## Configuration

### 1. Configure Repository Access

#### Public Repository (No authentication needed)

Public repositories work out of the box.

#### Private Repository

```bash
# Add repository with SSH key
argocd repo add git@github.com:username/repo.git --ssh-private-key-path ~/.ssh/id_rsa

# Add repository with username/password
argocd repo add https://github.com/username/repo.git --username myuser --password mypassword

# Add repository with token
argocd repo add https://github.com/username/repo.git --username myuser --password ghp_xxxxxxxxxxxxxxxxxxxx
```

### 2. Create Application

#### Via UI

1. Click "NEW APP"
2. Fill in application details:
   - Application Name: `myapp-argo-application`
   - Project: `default`
   - Repository URL: `https://github.com/armansheikhhosseini/gitops-argocd-demo.git`
   - Path: `dev`
   - Cluster: `https://kubernetes.default.svc`
   - Namespace: `myappmin`
3. Enable "AUTO-SYNC"
4. Click "CREATE"

#### Via CLI

```bash
# Create application
argocd app create myapp-argo-application \
  --repo https://github.com/armansheikhhosseini/gitops-argocd-demo.git \
  --path dev \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace myappmin \
  --sync-policy automated \
  --auto-prune \
  --self-heal

# Sync application
argocd app sync myapp-argo-application
```

#### Via Kubernetes Manifest

```bash
# Apply the ArgoCD Application manifest
kubectl apply -f argocd/application.yaml
```

## Monitoring

### Check Application Status

```bash
# Via kubectl
kubectl get applications -n argocd

# Via ArgoCD CLI
argocd app list
argocd app get myapp-argo-application

# Check sync status
argocd app sync myapp-argo-application --dry-run
```

### View Application Resources

```bash
# List all resources managed by the application
argocd app resources myapp-argo-application

# Get application details
argocd app describe myapp-argo-application
```

## Troubleshooting

### Common Issues

#### 1. Application Out of Sync

```bash
# Check differences
argocd app diff myapp-argo-application

# Force sync
argocd app sync myapp-argo-application --force

# Hard refresh
argocd app hard-refresh myapp-argo-application
```

#### 2. Repository Access Issues

```bash
# List repositories
argocd repo list

# Test repository connection
argocd repo get https://github.com/armansheikhhosseini/gitops-argocd-demo.git
```

#### 3. Resource Sync Issues

```bash
# Check application events
kubectl describe application myapp-argo-application -n argocd

# Check ArgoCD server logs
kubectl logs -f deployment/argocd-server -n argocd

# Check application controller logs
kubectl logs -f deployment/argocd-application-controller -n argocd
```

### Health Checks

```bash
# Check ArgoCD components
kubectl get pods -n argocd

# Check application health
argocd app health myapp-argo-application

# Check sync status
argocd app sync-status myapp-argo-application
```

## Best Practices

1. **Use App of Apps Pattern**: Manage multiple applications with a parent application
2. **Resource Quotas**: Set appropriate resource limits
3. **RBAC**: Configure proper access controls
4. **Monitoring**: Set up alerts for sync failures
5. **Backup**: Regular backup of ArgoCD configuration
6. **Git Structure**: Organize manifests in separate directories per environment

## Security Considerations

1. **TLS**: Always use HTTPS in production
2. **Authentication**: Configure SSO (LDAP, OIDC, etc.)
3. **Authorization**: Use RBAC policies
4. **Network Policies**: Restrict network access
5. **Secret Management**: Use external secret management tools

## Next Steps

- Configure notifications (Slack, email)
- Set up monitoring with Prometheus/Grafana
- Implement multi-environment deployments
- Configure webhook-based sync
