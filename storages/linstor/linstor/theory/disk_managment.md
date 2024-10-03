# Adding and Removing Disks
#### add a disk to the diskless resource backups on ‘alpha’:
```bash
linstor resource toggle-disk alpha backups --storage-pool pool_ssd
```
#### Remove this disk again
```bash
linstor resource toggle-disk alpha backups --diskless
```

## Migrating Disks Between Nodes
```bash
linstor resource create bravo backups --drbd-diskless
linstor resource toggle-disk bravo backups --storage-pool pool_ssd --migrate-from alpha
```
