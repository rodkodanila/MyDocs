# Install linstor
1.    The DRBD9 kernel module is installed and loaded.
2.    The drbd-utils package is installed.
3.    LVM tools are installed.
4.    linstor-controller or linstor-satellite packages and their dependencies are installed on appropriate nodes.
5.    The linstor-client is installed on the linstor-controller node.


```bash
apt install linstor-controller linstor-client -y
apt install linstor-satellite -y 
```

# Initializing Your Cluster
```bash
systemctl enable --now linstor-controller
linstor node list
```


# Configuring linstor utils 
```bash
linstor --controllers=nodename node list
# if https
linstor --controllers linstor+ssl://<controller-node-name-or-ip-address>
```
### Specifying Controllers in the LINSTOR Configuration File
```bash
/etc/linstor/linstor-client.conf
[global]
controllers=alice
```

# Adding Nodes to Your Cluster
```bash
linstor node create <name> <ip>
sudo systemctl enable --now  linstor-satellite
```
# Specifying LINSTOR Node Types
```bash
linstor node create bravo 10.43.70.3 --node-type combined
linstor node info
```

