# Backup and Restore Database
#### This tool has an executable file called /usr/share/linstor-server/bin/linstor-database. This executable has two subcommands, export-db and import-db
## Backing Up the Database
```bash
systemctl stop linstor-controller
/usr/share/linstor-server/bin/linstor-database export-db ~/db_export
systemctl start linstor-controller
cp ~/db_export <somewhere safe>
```
## Restoring the Database From a Backup
```bash
systemctl stop linstor-controller
/usr/share/linstor-server/bin/linstor-database import-db ~/db_export
systemctl start linstor-controller
```

