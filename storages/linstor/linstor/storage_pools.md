# Creating Storage Pools
When using LINSTOR together with LVM and DRBD, set the global_filter value in the LVM global configuration file (/etc/lvm/lvm.conf on RHEL) to:
```text
/etc/lvm/lvm.conf 
lobal_filter = [ "r|^/dev/drbd|" ]
```
This setting tells LVM to reject DRBD devices from operations such as scanning or opening attempts. In some cases, not setting this filter might lead to increased CPU load or stuck LVM operations.

# Create vg
```bash
vgcreate vg_linstor_disk /dev/nvme0n1 /dev/nvme1n1 [...]
```
# create a storage pool lvm
```bash
linstor storage-pool create lvm <nodename> <pool_name> <vg_name>
linstor storage-pool list | linstor sp l
```

# Sharing Storage Pools with Multiple Nodes (не понял зачем)
```bash
linstor storage-pool create lvm --external-locking \
  --shared-space O1btSy-UO1n-lOAo-4umW-ETZM-sxQD-qT4V87 \
  <nodename> pool_ssd shared_vg_ssd
```

# Creating Storage Pools by Using the Physical Storage Command
```bash
linstor physical-storage list
linstor physical-storage create-device-pool --pool-name lv_my_pool \
  LVMTHIN node_alpha /dev/vdc --storage-pool newpool
```

#  Resource Groups to Deploy LINSTOR Provisioned Volumes (method for deploying volumes )
```bash
linstor resource-group create my_ssd_group \
--storage-pool pool_ssd --place-count 2
# we can configure drbd options
linstor resource-group drbd-options --verify-alg crc32c my_verify_group
linstor volume-group create my_ssd_group
linstor resource-group spawn-resources my_ssd_group my_ssd_res 20G
```


