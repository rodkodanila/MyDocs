# Configuring Highly Available LINSTOR Database Storage
# 1. Creating a Resource Group For The HA LINSTOR Database Storage Resource
```bash
linstor resource-group create \
--storage-pool my-thin-pool \
--place-count 3 \
--diskless-on-remaining true \
linstor-db-grp
```
```bash
linstor resource-group drbd-options \
--auto-promote=no \
--quorum=majority \
--on-suspended-primary-outdated=force-secondary \
--on-no-quorum=io-error \
--on-no-data-accessible=io-error \
--rr-conflict=retry-connect \
linstor-db-grp
```
# 2. Creating a Volume Group For The HA LINSTOR Database Storage Resource
```bash
linstor volume-group create linstor-db-grp
```
# 3. Spawn resource
```bash
linstor resource-group spawn-resources linstor-db-grp linstor_db 200M
```
# 4. Moving the LINSTOR Database to HA Storage
```bash
systemctl disable --now linstor-controller
```
###  Next, create the systemd mount service.
```bash
cat << EOF > /etc/systemd/system/var-lib-linstor.mount
[Unit]
Description=Filesystem for the LINSTOR controller

[Mount]
# you can use the minor like /dev/drbdX or the udev symlink
What=/dev/drbd/by-res/linstor_db/0
Where=/var/lib/linstor
EOF
mv /var/lib/linstor{,.orig}
mkdir /var/lib/linstor
chattr +i /var/lib/linstor # only if on LINSTOR >= 1.14.0
drbdadm primary linstor_db
mkfs.ext4 /dev/drbd/by-res/linstor_db/0
systemctl start var-lib-linstor.mount
cp -r /var/lib/linstor.orig/* /var/lib/linstor
systemctl start linstor-controller
```

# 5.  Installing Multiple LINSTOR Controllers
####  It is important that the controllers are manged by drbd-reactor, so verify that the linstor-controller.service is disabled on all nodes! 
#### systemctl disable linstor-controller on all cluster nodes and systemctl stop linstor-controller on all nodes except the one it is currently running from the previous step.
#### For starting and stopping the mount service and the linstor-controller service, use DRBD Reactor.

### /etc/drbd-reactor.d/linstor_db.toml
```text
[[promoter]]
id = "linstor_db"
[promoter.resources.linstor_db]
start = ["var-lib-linstor.mount", "linstor-controller.service"]
```
```bash
systemctl restart drbd-reactor
systemctl enable drbd-reactor

systemctl edit linstor-satellite
[Service]
Environment=LS_KEEP_RES=linstor_db
```

```bash 
systemctl restart linstor-satellite
```

# DRBD Clients
#### By using the --drbd-diskless option instead of --storage-pool you can have a permanently diskless DRBD device on a node. This means that the resource will appear as block device and can be mounted to the file system without an existing storage-device. The data of the resource is accessed over the network on another node with the same resource.

```bash
linstor resource create delta backups --drbd-diskless
```

#  DRBD Consistency Groups (Multiple Volumes within a Resource)
#### To deploy more than one volume in a LINSTOR-resource you have to create two volume-definitions with the same name.
```bash
linstor volume-definition create backups 500G
linstor volume-definition create backups 100G
```

# Placing Volumes of One Resource in Different Storage Pools
```bash
linstor resource-definition create backups
linstor volume-definition create backups 500G
linstor volume-definition create backups 100G
linstor volume-definition set-property backups 0 StorPoolName pool_hdd
linstor volume-definition set-property backups 1 StorPoolName pool_ssd
linstor resource create <node_name> backups
```

