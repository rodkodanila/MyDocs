# Managing Network Interface Cards
```bash
linstor node interface create node-0 10G_nic 192.168.43.231
linstor node set-property node-0 PrefNic 10G_nic
linstor node interface modify node-0 1G-satconn --active
```

# Creating Multiple DRBD Paths with LINSTOR
```bash
linstor node interface create alpha nic1 192.168.43.221
linstor node interface create alpha nic2 192.168.44.221
linstor node interface create bravo nic1 192.168.43.222
linstor node interface create bravo nic2 192.168.44.222
linstor resource-connection path create node1 node2 myResource path1 nic1 nic1
linstor resource-connection path create node1 node2 myResource path2 nic2 nic2
```

### Example drbd config after this 
```text
resource myResource {
  ...
  connection {
    path {
      host alpha address 192.168.43.221:7000;
      host bravo address 192.168.43.222:7000;
    }
    path {
      host alpha address 192.168.44.221:7000;
      host bravo address 192.168.44.222:7000;
    }
  }
}
```
# How Adding a New DRBD Path Affects the Default Path
## TCP use one path a timem RDMA all pathes
```bash
linstor resource-connection path create alpha bravo myResource path3 default default
```




