# argocd-apps

This is a **public Argo CD "App of Apps" repository**.
It defines which applications should be deployed in the cluster, and from where.

## Structure
- `root-app.yaml` → The **App of Apps** entrypoint for Argo CD.
- `projects/` → Argo CD **AppProjects** (security boundaries for dev and prod).
- `apps/` → Applications or ApplicationSets for each app (e.g., nginx).

## Namespace policy
Projects are configured to allow deployments **only to namespaces that end with**:
- `-dev` for development
- `-prod` for production

## Bootstrap Instructions

**Install Argo CD** (if not already):
   ```bash
   kubectl create ns argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

sync
```
argocd login argocd.lan --username admin --password $ARGOCD_PASSWORD --grpc-web --insecure
argocd repo add git@github.com:JCTekDev/k8s-cluster-01-apps-default.git --ssh-private-key-path ~/.ssh/id_rsa
```


Criar o Secret com a chave privada
Arquivo repo-creds-github.yaml (exemplo global para todos os repos de GitHub):
```
k apply -f repo-creds-github.yaml -n argocd
```

Update known_hosts (senão pode dar host key verification failed)
```
ssh-keyscan github.com >> /tmp/ssh_known_hosts
kubectl -n argocd create configmap argocd-ssh-known-hosts-cm --from-file=ssh_known_hosts=/tmp/ssh_known_hosts --dry-run=client -o yaml | kubectl apply -f -
```

ssh-keyscan -p 443 ssh.github.com >> /tmp/ssh_known_hosts

Apply root-app.yaml
```
kubectl -n argocd apply -f root-app.yaml
```
