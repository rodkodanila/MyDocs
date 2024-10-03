# Backup and Restore Database
```bash
systemctl stop linstor-controller
/usr/share/linstor-server/bin/linstor-database export-db ~/db_export
systemctl start linstor-controller
cp ~/db_export <somewhere safe>
```
# Restoring the Database From a Backup
```bash
systemctl stop linstor-controller
/usr/share/linstor-server/bin/linstor-database import-db ~/db_export
systemctl start linstor-controller
```

