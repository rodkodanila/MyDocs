# 1. Labeled nodes
```bash
kubectl label nodes node-1 ingress-nginx-node=enable
kubectl label nodes node-2 ingress-nginx-node=enable
```
# 2. apply nginx controller
```bash
k apply -f nginx-ingress-controller.yaml
```
