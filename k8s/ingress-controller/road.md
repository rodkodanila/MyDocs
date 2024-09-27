# 1. Labeled nodes
```bash
kubectl label nodes node-1 ingress-nginx-node=enable
kubectl label nodes node-2 ingress-nginx-node=enable
```

# 2. Configure nginx
# открывает порт только на nodes с ingress-controller и трафик идет строго на Pod на этой Node
  externalTrafficPolicy: Local
# add json log format  
    log-format-escape-json: "true"
    log-format-upstream: '{"time": "$time_iso8601", "remote_addr": "$proxy_protocol_addr", "x-forward-for": "$proxy_add_x_forwarded_for", "request_id": "$req_id", "remote_user": "$remote_user", "bytes_sent": $bytes_sent, "request_time": $request_time, "status":$status, "vhost": "$host", "request_proto": "$server_protocol", "path": "$uri", "request_query": "$args", "request_length": $request_length, "duration": $request_time,"method": "$request_method", "http_referrer": "$http_referer", "http_user_agent": "$http_user_agent", "namespace": "$namespace", "ingress_name": "$ingress_name", "service_name": "$service_name", "service_port": "$service_port" }'
# Change Кол-во экземпляров Ingress Controller
  replicas: 2

# Запускаться только на тех нодах где label
# kubectl label nodes node-1 ingress-nginx-node=enable
  nodeSelector:
     ingress-nginx-node: enable

# ingress-class чтобы указывать в ingress явно , для какого controller конфиг
 - --ingress-class=nginx
# Явно следит за конкретным namespace, для поиска ingress
 - --watch-namespace=mynamespace


# 3. apply nginx controller
```bash
k apply -f nginx-ingress-controller.yaml
```
