# Storage Pools

## Creating Storage Pools
### 1. change lvm settings 
#### This setting tells LVM to reject DRBD devices from operations such as scanning or opening attempts. In some cases, not setting this filter might lead to increased CPU load or stuck LVM operations.
```text
global_filter = [ "r|^/dev/drbd|" ]
```
### 2. Create lvm group
```bash
vgcreate vg_hdd /dev/sdb 
# or
linstor physical-storage create-device-pool --pool-name lv_thin_pool LVMTHIN linstor1 /dev/sdb --storage-pool thin_storage_pool
linstor physical-storage create-device-pool --pool-name lv_thin_pool LVMTHIN linstor2 /dev/sdb --storage-pool thin_storage_pool
linstor physical-storage create-device-pool --pool-name lv_thin_pool LVMTHIN linstor3 /dev/sdb --storage-pool thin_storage_pool
```
### 3. Check list of storage pools
```bash
linstor sp l
```
## Using Resource Groups to Deploy LINSTOR Provisioned Volumes
### 1. Create resource-group
```bash
linstor resource-group create my_test_resource_group --storage-pool thin_storage_pool --place-count 2
```
### 2. Create volume group
```bash
linstor volume-group create my_test_volume_group
```
### 3. Spawn resource 
```bash
linstor resource-group spawn-resources my_test_volume_group my_test_resource_group 100M
```
### We can change drbd options 
```bash
linstor resource-group drbd-options --verify-alg crc32c my_verify_group
```
