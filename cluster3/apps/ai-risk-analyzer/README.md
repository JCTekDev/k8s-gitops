# AI Risk Analyzer - ArgoCD Applications

This folder contains ArgoCD Application manifests for deploying AI Risk Analyzer in cluster3.

## Contents

- **ai-risk-analyzer-dev.yaml** - Development environment deployment

## Prerequisites

- ArgoCD installed and configured
- SSH key configured for GitHub (git@github.com:JCTekDev/k8s-manifests-public.git)
- SealedSecrets controller installed in the cluster
- k8s-manifests-public repository accessible

## Configuration

### Namespace

- **Development**: `ai-risk-analyzer-dev`

### Repository

- **Source**: `git@github.com:JCTekDev/k8s-manifests-public.git`
- **Path**: `ai-risk-analyzer/overlays/dev`
- **Branch**: `main`

## Deployment

The application is automatically deployed via the root-app in cluster3. It will:

1. Create the `ai-risk-analyzer-dev` namespace
2. Deploy the Sealed Secrets controller ConfigMap
3. Create ConfigMap with environment variables
4. Deploy the AI Risk Analyzer service
5. Expose via ClusterIP service on port 80

## Synchronization

- **Prune**: Enabled - Resources deleted from Git are removed from cluster
- **Self-heal**: Enabled - Automatic sync when cluster state drifts from Git
- **Auto-sync**: Enabled - Changes in main branch are automatically applied

## Secrets Management

Sensitive data (JOGET_USERNAME, JOGET_PASSWORD, OPENAI_API_KEY) are stored as SealedSecrets in the k8s-manifests-public repository.

To update secrets:

1. Navigate to `k8s-manifests-public/ai-risk-analyzer/overlays/dev`
2. Run: `./ai-risk-analyzer-secrets-sealer.sh`
3. Commit the generated `ai-risk-analyzer-secrets.clusterwide.sealed.yaml`
4. Push to main branch
5. ArgoCD will automatically sync

## Monitoring

### View Application Status

```bash
argocd app get ai-risk-analyzer
```

### View Logs

```bash
kubectl logs -n ai-risk-analyzer-dev -l app=ai-risk-analyzer -f
```

### Port Forward

```bash
kubectl port-forward -n ai-risk-analyzer-dev svc/ai-risk-analyzer 8000:80
```

Then access: http://localhost:8000/docs

## Troubleshooting

### Application out of sync

ArgoCD will automatically sync with `selfHeal: true`. To manually sync:

```bash
argocd app sync ai-risk-analyzer
```

### Secrets not unsealing

Verify SealedSecrets controller is running:

```bash
kubectl get pods -n kube-system -l app.kubernetes.io/name=sealed-secrets
```

### Pod not starting

Check events and logs:

```bash
kubectl describe pod -n ai-risk-analyzer-dev -l app=ai-risk-analyzer
kubectl logs -n ai-risk-analyzer-dev -l app=ai-risk-analyzer
```

## Resource Limits

Current configuration:
- **CPU Requests**: 100m
- **CPU Limits**: 500m
- **Memory Requests**: 256Mi
- **Memory Limits**: 512Mi

Adjust in k8s-manifests-public if needed.

## References

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [SealedSecrets Documentation](https://github.com/bitnami-labs/sealed-secrets)
- [Application Manifest](../../../k8s-manifests-public/ai-risk-analyzer/)
