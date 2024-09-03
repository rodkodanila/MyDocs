# 1. Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.1.1/manifests/install.yaml
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: repo-server-ssh-cm
data:
  config: |
    HOST vs-ssh.visualstudio.com
      HostkeyAlgorithms +ssh-rsa
      PubkeyAcceptedAlgorithms +ssh-rsa


argocd-server Deployment
- mountPath: /app/config/ssh/config
          name: repo-server-ssh-cm
          subPath: ssh_config
volumes:
      - name: repo-server-ssh-cm
        configMap: 
          name: repo-server-ssh-cm
```

# 2. Download argocd cli
```
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
argocd version   -   check version
```

# 3. Add access on WEB 
## nodeport
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```
## prot-forward
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

# 4. Change standart password
## get passwd
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d ; echo
```
```
argocd login <NodeIP>:<NodePort> --grpc-web            <- login
argocd account update-password --grpc-web              <- change password

```
# 5. Create local user
## add user
```
kubectl apply -f argocd-add-user.yaml
```
## add rbac
```
kubectl apply -f argocd-add-rbac.yaml
```
## user list
```
argocd account list --grpc-web
```
## create passsword for new user
```
argocd account update-password --account <name> --grpc-web
```
## auth 
```
argocd login <IP>:<Port> --grpc-web
argocd cluster list
```

# 6. add repo to argocd
```
ssh-keygen -t rsa-sha2-512
argocd repo add git@<git-path> --ssh-private-key-path ~/.ssh/azure --upsert --insecure-skip-server-verification
```