# Architecture Diagram

## Current ASCII Diagram (in README.md)
The README currently has a text-based architecture diagram.

## Recommended: Create Visual Diagram

### Tools to Use:
1. **Draw.io** (https://app.diagrams.net/) - Free, web-based
2. **Lucidchart** - Professional diagramming
3. **Canva** - Simple drag-and-drop interface
4. **PowerPoint/Google Slides** - Basic but effective

### Diagram Elements to Include:

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Developer  │───▶│   GitHub    │───▶│ GitHub      │
│   (Code)    │    │ Repository  │    │ Actions     │
└─────────────┘    └─────────────┘    └─────────────┘
                                              │
                                              ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ Kubernetes  │◀───│   ArgoCD    │◀───│ Docker Hub  │
│  Cluster    │    │  (GitOps)   │    │ (Registry)  │
└─────────────┘    └─────────────┘    └─────────────┘
```

### Steps to Create:
1. Open Draw.io
2. Create boxes for: Developer, GitHub, GitHub Actions, Docker Hub, ArgoCD, Kubernetes
3. Add arrows showing the flow
4. Use colors: Blue for code, Green for CI/CD, Orange for deployment
5. Save as PNG
6. Upload to `/images/architecture-diagram.png`

### Key Flow to Show:
1. Developer pushes code to GitHub
2. GitHub Actions triggers CI/CD
3. Pipeline builds Docker image
4. Image pushed to Docker Hub
5. ArgoCD detects changes in Git
6. ArgoCD deploys to Kubernetes
7. Application runs in cluster

This visual will make your repository much more professional and easier to understand!
