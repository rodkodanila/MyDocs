# 1. Create argocd namespace
```bash
kubectl create namespace argocd
```
# 2. Install cert-manager
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.3/cert-manager.yaml
```

# 3. Install ArgoCD.yaml and add --insecure to argocd-server, because of ingress-ontroller and ssl termination
```bash
wget https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
#### containers:
#### - args:
#### - /usr/local/bin/argocd-server
#### - --insecure
#### - /argocd

# 4. Apply argocd
```bash
kubectl -n argocd apply -f install.yaml
```

# 5. Configure Ingress for argocd-server
```bash
kubectl -n argocd apply -f ingress-argocd-ip.yaml
```

# 6. Install argocd cli
```bash
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
argocd version
```
# 7. Change standart admin password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d ; echo
# insert standart admin password from secret
argocd login 192.168.138.148:30443 --grpc-web
# update admin password
argocd account update-password --grpc-web
```
# 8. Add users to argocd
### Данные хранятся в ConfigMap - добавление пользователей это добавление записи в ConfigMap
```bash
kubectl apply -f create_argo_cd_user_cm.yaml
# Добавить RBAC argocd
kubectl apply -f create_argocd_rbac_cm.yaml
# users list
argocd account list --grpc-web
# change password for new user
argocd account update-password --account user --grpc-web
```

