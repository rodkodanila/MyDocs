# Configuring a Cluster
# Creating and Deploying Resources and Volumes
# 1. create a new resource definition
```bash
linstor resource-definition create backups
```
# 2. create a new volume definition within that resource definition
```bash
linstor volume-definition create backups 500G
```
### resize
The parameter 0 is the number of the volume in the resource backups
```bash
linstor volume-definition set-size backups 0 100G
```
# 3. Manually Placing Resources
```bash
linstor resource create <node{1..3}> backups --storage-pool pool_hdd
```
# 4. Storage Pool Placement
```bash
linstor resource-group create backups --place-count 3 --storage-pool pool_hdd
```
without --storage-pool  linstor choose on his own pool, when you will spawn resource

# The remaining storage pools will be rated by different strategies.
```text
1. MaxFreeSpace
2. MinReservedSpace
3. MinRscCount
4. MaxThroughput
```
weight 0-1
```bash
linstor controller set-property Autoplacer/Weights/MaxFreeSpace <weight>
linstor controller set-property Autoplacer/Weights/MinReservedSpace <weight>
linstor controller set-property Autoplacer/Weights/MinRscCount <weight>
linstor controller set-property Autoplacer/Weights/MaxThroughput <weight>
```
This step also considers other auto-placement restrictions such as --replicas-on-same, --replicas-on-different, --do-not-place-with, --do-not-place-with-regex, --layer-list, and --providers.

# Unsetting Autoplacement Properties
```text
linstor node list-properties node-1
linstor resource-group modify <resource-group-name> --<autoplacement-property>
```

#  Deleting Resources, Resource Definitions, and Resource Groups
```bash
linstor resource-definition delete <resource_definition_name>
```

# Deleting a Resource
```bash
linstor resource delete <node_name> <resource_name>
```
# Deleting a Resource Group
```bash
linstor resource-group delete <resource_group_name>
```
