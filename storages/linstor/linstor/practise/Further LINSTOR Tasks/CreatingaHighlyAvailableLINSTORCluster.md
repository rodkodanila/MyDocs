# Creating a Highly Available LINSTOR Cluster
## Configuring Highly Available LINSTOR Database Storage
### 1. Creating a Resource Group For The HA LINSTOR Database Storage Resource
resource-group - descriptions like storage class for k8s, describe all layers
```bash
linstor resource-group create \
--storage-pool my-thin-pool \
--place-count 3 \
--diskless-on-remaining true \
linstor-db-grp
```
#### set drbd options
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
### 2. Creating a Volume Group For The HA LINSTOR Database Storage Resource
```bash
linstor volume-group create linstor-db-grp
```
### 3. Creating a Resource For The HA LINSTOR Database Storage
```bash
linstor resource-group spawn-resources linstor-db-grp linstor_db 200M
```
### 4. Moving the LINSTOR Database to HA Storage
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
chattr +i /var/lib/linstor
drbdadm primary linstor_db
mkfs.ext4 /dev/drbd/by-res/linstor_db/0
systemctl start var-lib-linstor.mount
cp -r /var/lib/linstor.orig/* /var/lib/linstor
systemctl start linstor-controller
```
##### Copy the /etc/systemd/system/var-lib-linstor.mount mount file to all the cluster nodes that you want to have the potential to run the LINSTOR controller service 
### 5. Installing Multiple LINSTOR Controllers
