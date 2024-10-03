# Working With Resource Snapshots and Backups

# Creating a Snapshot
```bash
linstor snapshot create resource1 snap1
```
#### This will create a snapshot on all nodes where the resource is present. LINSTOR will create consistent snapshots, even when the resource is in active use.
#### 15 minutes
#### LINSTOR will keep the last 10 snapshots by default.
```bash
linstor resource-definition set-property AutoSnapshot/RunEvery 15
linstor resource-definition set-property AutoSnapshot/Keep 5
```
#  Restoring a Snapshot
```bash
linstor resource-definition create resource2
linstor snapshot volume-definition restore --from-resource resource1 \
  --from-snapshot snap1 --to-resource resource2
```
### create resources based on the snapshots
```bash
 linstor snapshot resource restore --from-resource resource1 \
  --from-snapshot snap1 --to-resource resource2
```
#### This will place the new resource on all nodes where the snapshot is present. The nodes on which to place the resource can also be selected explicitly

# Rolling Back to a Snapshot
```bash
linstor snapshot rollback resource1 snap1
```
# Removing a Snapshot
```bash
linstor snapshot delete resource1 snap1
```
# Shipping Snapshots
Snapshots can be shipped between nodes in the same LINSTOR cluster, between different LINSTOR clusters, or to S3 storage such as Amazon S3 or MinIO.
## Working With Snapshot Shipping Remotes
#### Creating an S3 Remote
```bash
linstor remote create s3 myRemote s3.us-west-2.amazonaws.com \
  my-bucket us-west-2 admin password
```
# Creating a LINSTOR Remote (for shiping snapshot between  cluster)
```bash
linstor remote create linstor myRemote 192.168.0.15
```
# Listing, Modifying, and Deleting Remotes
```bash
linstor remote list
linstor remote delete myRemoteName
linstor remote modify
```
# Specifying a LINSTOR Passphrase When Creating a Remote
```bash
linstor --controllers <TARGET_CONTROLLER> remote create linstor \
--cluster-id <SOURCE_CLUSTER_ID> --passphrase <SOURCE_CONTROLLER_PASSPHRASE> <NAME> <URL>
```
```bash
linstor --controllers <SOURCE_CONTROLLER> remote create linstor \
--cluster-id <TARGET_CLUSTER_ID> --passphrase <TARGET_CONTROLLER_PASSPHRASE> <NAME> <URL>
```
# Shipping Snapshots to an S3 Remote
```bash 
linstor backup create myRemote myRsc
```
# Shipping Snapshots to a LINSTOR Remote
```bash
linstor remote create linstor --cluster-id <SOURCE_CLUSTER_ID> <NAME> <URL>
```
# Shipping a Snapshot of a Resource to a LINSTOR Remote
```bash
linstor backup ship myRemote localRsc targetRsc
```
# Snapshot Shipping Within a Single LINSTOR Cluster
```bash
linstor remote create linstor --cluster-id <CLUSTER_ID> <NAME> localhost
```
# Listing Backups on a Remote
```bash
linstor backup list myS3Remote
```
# Deleting Backups on a Remote
```bash
linstor backup delete all myRemote
linstor backup delete id myRemote my-rsc_back_20210824_072543
linstor backup delete filter myRemote 
# -t 20210914_120000 will delete all backups made before 12 o’clock on the 14th of September, 2021.\
# -n myNode will delete all backups uploaded by the given node. \
# -r myRsc will delete all backups with the given resource name. \
# These filters can be combined as needed. Finally, \
# --cascade deletes not only the selected backup(s), \
# but all other incremental backups depending on any of the selected backups.

linstor backup delete s3key myRemote randomPictureInWrongBucket
```
# Restoring Backups From a Remote
```bash
linstor backup restore 
 linstor backup restore myRemote myNode targetRsc --resource sourceRsc
 linstor backup restore myRemote myNode targetRsc --id sourceRsc_back_20210824_072543
```
# Shipping a Snapshot in the Same Cluster
```bash
linstor remote create linstor self 127.0.0.1 --cluster-id <LINSTOR_CLUSTER_ID>
```

#  Scheduled Backup Shipping
```bash
 linstor schedule create \
  --incremental-cron '* * * * *' \ (1)
  --keep-local 5 \ (2)
  --keep-remote 4 \ (3)
  --on-failure RETRY \ (4)
  --max-retries 10 \ (5)
  <schedule_name> \ (6)
  '* * * * *' # full backup cron schema (7)
```
# Listing a Backup Shipping Schedule
```bash
linstor schedule list
```
# Enabling Scheduled Backup Shipping
```bash
linstor backup schedule enable \
  [--node source_node] \ (1)
  [--rg resource_group_name | --rd resource_definition_name] \ (2)
  remote_name \ (3)
  schedule_name (4)
```

# Disabling a Backup Shipping Schedule
```bash
 linstor backup schedule disable \
  [--rg resource_group_name | --rd resource_definition_name] \
  remote_name \ (3)
  schedule_name (4)
```

