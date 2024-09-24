# 1. Labeled nodes
```bash
kubectl label nodes node-1 ingress-nginx-node=enable
```
# 2. apply nginx controller
k apply -f nginx-ingress-controller.yaml
