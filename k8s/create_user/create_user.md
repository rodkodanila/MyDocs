# 1.  Generating SSL Certificates for Users SSL certificates are essential for user authentication in Kubernetes
```bash
sudo openssl genrsa -out user1.key 2048
sudo openssl req -new -key user1.key -out user1.csr
sudo openssl x509 -req -in user1.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out user1.crt -days 500
```
## Organization name = namespace → “test-organization”
## And Username = common name → “user1"


