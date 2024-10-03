# Creating and Deploying Resources and Volumes
####  example scenario, assume that you have a goal of creating a resource named backups with a size of 500GiB that is replicated among three cluster nodes
### 1. create a new resource definition
```bash
linstor resource-definition create backups
```
### 2. create a new volume definition within that resource definition
```bash
linstor volume-definition create backups 10M
```
### 3. If you want to resize
```bash
linstor volume-definition set-size backups 0 5M
```

## Manually Placing Resources
```bash
linstor resource create linstor1 backups --storage-pool thin_storage_pool
linstor resource create linstor2 backups --storage-pool thin_storage_pool
linstor resource create linstor3 backups --storage-pool thin_storage_pool
```

## Storage Pool Placement
```bash
linstor resource-group create backups --place-count 3 --storage-pool thin_storage_pool
```
## We can set properties for autoplacer (weight = 0 - 1)
```bash
linstor controller set-property Autoplacer/Weights/MaxFreeSpace <weight>
linstor controller set-property Autoplacer/Weights/MinReservedSpace <weight>
linstor controller set-property Autoplacer/Weights/MinRscCount <weight>
linstor controller set-property Autoplacer/Weights/MaxThroughput <weight>
```
###  Check properties on node
```bash
linstor node list-properties linstor1
```

## Constraining Automatic Resource Placement by LINSTOR Layers or Storage Pool Providers
#### we are enabling luks and drbd like layers for this r-g
```bash
linstor resource-group create my_luks_rg --place-count 3 --layer-list drbd,luks
```

## Deleting a Resource Definition
```bash
linstor resource-definition delete <resource_definition_name>
```
## Deleting a Resource
```bash
linstor resource delete <node_name> <resource_name>
```
## Deleting a Resource Group
```bash
linstor resource-group delete <resource_group_name>
```
## Check existing resource definitions
```bash
linstor resource-definition list
```
