# Images Directory

This directory contains screenshots and diagrams for the GitOps demo.

## Recommended Images to Add:

### 1. Architecture Diagram
- `architecture-diagram.png` - GitOps workflow overview
- Shows: Developer → GitHub → CI/CD → Registry → ArgoCD → Kubernetes

### 2. ArgoCD Screenshots
- `argocd-dashboard.png` - ArgoCD main dashboard
- `argocd-application-view.png` - Application details in ArgoCD
- `argocd-sync-status.png` - Sync status and health
- `argocd-topology.png` - Application topology view

### 3. Application Screenshots
- `application-running.png` - Java application running
- `kubernetes-resources.png` - kubectl get all output

### 4. CI/CD Pipeline
- `github-actions-pipeline.png` - GitHub Actions workflow
- `docker-hub-image.png` - Docker image in registry

## How to Add Screenshots:

1. **Take Screenshots** of:
   - ArgoCD UI with your application deployed
   - Your Java application running (localhost:8080)
   - GitHub Actions workflow runs
   - kubectl commands showing deployed resources

2. **Save Screenshots** in this directory with descriptive names

3. **Update README.md** to include the images using:
   ```markdown
   ![ArgoCD Dashboard](images/argocd-dashboard.png)
   ```

## Architecture Diagram
You can create a simple architecture diagram using:
- Draw.io (diagrams.net)
- Lucidchart
- Even PowerPoint or Google Slides

The diagram should show the complete GitOps flow from code commit to deployment.
