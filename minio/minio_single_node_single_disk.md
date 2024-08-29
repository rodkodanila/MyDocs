# 1. make disk (XFS)
```
apt-get install xfsprogs
/sbin/mkfs.xfs /dev/vdb
mkdir /data_minio
```
```
cat << EOF >> /etc/fstab
/dev/vdb  /data_minio  xfs  defaults,noatime  0  0
EOF
```
```
mount -a
```

# 2. Install minio
```
wget https://dl.min.io/server/minio/release/linux-amd64/minio_20240826153307.0.0_amd64.deb
export PATH=$PATH:/sbin
dpkg -i minio*.deb
```
# 3. Create User and Add Permissions
```
groupadd -r minio-user
useradd -M -r -g minio-user minio-user
chown minio-user:minio-user /data_minio
```
# 4. Create defaul envs
```
cat << EOF > /etc/default/minio
MINIO_ROOT_USER=<username>
MINIO_ROOT_PASSWORD=<password>
MINIO_VOLUMES="/data_minio"
MINIO_OPTS="--console-address :9001"
EOF
```
# 5. Start/Enable service
```
systemctl start minio.service
systemctl enable minio.service
systemctl status minio.service
```

# 6. Install Minio client
```
curl https://dl.min.io/client/mc/release/linux-amd64/mc \
  --create-dirs \
  -o /usr/local/bin/mcc
chmod +x /usr/local/bin/mcc
```

# 7. Configure minio client
```
mcc alias set <cluster-name> http://localhost:9000 <accesskey> <secretkey>
mcc admin info <cluster-name> - check cluster info
mcc ls <cluster-name> - check list of backets
```