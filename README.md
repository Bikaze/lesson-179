# ArgoCD Notifications

This repository contains Kubernetes manifests for deploying applications using ArgoCD with GitOps principles.

## Overview

This project demonstrates a GitOps workflow using ArgoCD for continuous deployment. ArgoCD monitors this repository and automatically syncs the declared Kubernetes resources to your cluster.

## Repository Structure

```
.
├── README.md              # Project documentation
└── nginx/
    └── deployment.yaml    # Nginx deployment manifest
```

## Prerequisites

- Kubernetes cluster (v1.20+)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) installed on the cluster
- `kubectl` CLI configured to access your cluster

## Components

### Nginx Deployment

The `nginx/deployment.yaml` contains a Kubernetes Deployment resource that:

- Deploys a single replica of nginx (version 1.25.1)
- Exposes port 80 for HTTP traffic
- Runs in the `default` namespace

## Usage

### Manual Deployment

To deploy the nginx application manually:

```bash
kubectl apply -f nginx/deployment.yaml
```

### ArgoCD Deployment

1. **Create an ArgoCD Application** pointing to this repository:

   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: nginx-app
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: https://github.com/Bikaze/lesson-179
       targetRevision: HEAD
       path: nginx
     destination:
       server: https://kubernetes.default.svc
       namespace: default
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

2. **Apply the ArgoCD Application**:

   ```bash
   kubectl apply -f argocd-application.yaml
   ```

3. **Verify the deployment**:

   ```bash
   kubectl get deployments -n default
   kubectl get pods -n default
   ```

## Configuration

### Customizing the Nginx Deployment

You can modify the `nginx/deployment.yaml` file to:

- **Change replicas**: Update the `spec.replicas` field
- **Update nginx version**: Modify the `image` field (e.g., `nginx:1.26.0`)
- **Add resource limits**: Include `resources.limits` and `resources.requests`
- **Configure environment variables**: Add `env` section to the container spec

### Example with Resource Limits

```yaml
containers:
  - name: nginx
    image: nginx:1.25.1
    ports:
      - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

## ArgoCD Notifications

ArgoCD supports notifications to alert you about application sync status changes. To configure notifications:

1. Install the ArgoCD Notifications controller
2. Configure notification triggers and templates
3. Set up destinations (Slack, email, webhook, etc.)

For more details, refer to the [ArgoCD Notifications documentation](https://argocd-notifications.readthedocs.io/en/stable/).

## Troubleshooting

### Common Issues

1. **Pod not starting**: Check pod events with `kubectl describe pod <pod-name>`
2. **Sync failed in ArgoCD**: Review the ArgoCD UI or CLI for sync errors
3. **Image pull errors**: Verify network connectivity and image availability

### Useful Commands

```bash
# Check deployment status
kubectl get deployments -n default

# View pod logs
kubectl logs -l app=nginx -n default

# Check ArgoCD application status
argocd app get nginx-app
```

## Contributing

1. Fork this repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is open source.
