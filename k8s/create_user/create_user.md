# 1.  Generating SSL Certificates for Users SSL certificates are essential for user authentication in Kubernetes
```bash
sudo openssl genrsa -out user1.key 2048
sudo openssl req -new -key user1.key -out user1.csr
sudo openssl x509 -req -in user1.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out user1.crt -days 500
```
## Organization name = namespace → “test-organization”
## And Username = common name → “user1"

# 2. Create role
```bash
kubectl apply -f role.yaml
```
# 3. Create RoleBindinf
```bash
kubectl apply -f rolebinding.yaml
```

# 4. Add context and user in config 
```bash
export KUBECONFIG=myconfig
base64 /root/test-organization/user1.crt > user1.crt.base64
base64 /root/test-organization/user1.key > user1.key.base64

base64 -w 0 /etc/kubernetes/ca.crt > ca.crt.base64
CA_CERT=$(cat ca.crt.base64)
IP=ip
PORT=

kubectl config set-cluster kubernetes \
    --server=https://${IP}:${PORT} \
    --certificate-authority-data="$CA_CERT" \
    --embed-certs=true
    
CLIENT_CERT=$(cat user1.crt.base64)
CLIENT_KEY=$(cat user1.key.base64)


kubectl config set-credentials dev --client-certificate-data="$CLIENT_CERT" --client-key-data="$CLIENT_KEY"
kubectl config set-context dev-context --cluster=kubernetes --namespace=ns --user=dev

cat myconfig 
```

# 5. Check cert
```bash
kubectl get pods  --kubeconfig=/root/test-organization/myconfig 
```