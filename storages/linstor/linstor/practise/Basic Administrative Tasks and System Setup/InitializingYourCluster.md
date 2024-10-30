# Initializing Your Cluster
```text
1.  The DRBD9 kernel module is installed and loaded.
2.  The drbd-utils package is installed.
3.  LVM tools are installed.
4.  linstor-controller or linstor-satellite packages and their dependencies are installed on appropriate nodes.
5.  The linstor-client is installed on the linstor-controller node.
```

## install linstor-controller on controller nodes
```bash
sudo apt install linstor-controller -y
```
## install linstor-satellite on worker nodes
```bash
sudo apt install linstor-satellite -y
```
## install linstor-client on managment nodes
```bash
sudo apt install linstor-client -y
```
### configure client
```bash
cat << EOF >> ~/.bashrc
alias linstor="linstor --controllers=linstor1"
EOF
```

## Start linstor-controller
```bash
systemctl enable --now linstor-controller
```

### Add nodes into cluster
```bash
linstor node create linstor1
linstor node create linstor2
linstor node create linstor3
```
### Start linstor-sattelite
```bash
systemctl enable --now linstor-satellite
```

## Check cluster health and info
```bash
linstor node list 
linstor node info
```