# 1. Add creds for repo in k8s
```bash
cat << EOF > nexus.json
{
  "auths": {
    "ivc-nexus-int.ivcmf.by:9876": {
      "username": "builder",
      "password": "E6Tb#z!422",
      "email": "test@test.ru",
      "auth": "YnVpbGRlcjpFNlRiI3ohNDIy"
    }
  }
}
EOF
kubectl create secret -n dev-pud generic nexus-int-creds \
    --from-file=.dockerconfigjson=nexus.json \
    --type=kubernetes.io/dockerconfigjson 
```
# 2. Add ImagePullSecret to Deployment or Pod
#### imagePullSecrets:
####  - name: regcred

