# 1. Check cluster status on node
```bash
rabbitmqctl cluster_status
```

# 2. Chagne node name env variable and add this hosts to /etc/hosts
```bash
vim /etc/rabbitmq/rabbitmq-env.conf
```
# 3. Stop rabbitmq service
```bash
systemctl stop rabbitmq-server.service
```

# 4. Copy erlang cookie
```bash
#on master
cat /var/lib/rabbitmq/.erlang.cookie
# on slaves
rabbitmqctl stop_app
vim /var/lib/rabbitmq/.erlang.cookie
systemctl restart rabbitmq-server
```

# 5. add node to cluster
```bash
# on master
rabbitmqctl stop_app
# on slave
rabbitmqctl join_cluster node01-rabbit@vm1
rabbitmqctl start_app
rabbitmqctl cluster_status
```