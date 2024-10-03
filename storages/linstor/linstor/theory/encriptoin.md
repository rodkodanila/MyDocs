# Encrypted Volumes
```text
Basic steps to use encryption:
1.    Create a master passphrase
2.    Add luks to the layer-list. Note that all plugins (e.g., Proxmox) require a DRBD layer as the top most layer if they do not explicitly state otherwise.
3.    Don’t forget to re-enter the master passphrase after a controller restart.
```
```bash
linstor encryption create-passphrase
# If you ever want to change the master passphrase this can be done with:
linstor encryption modify-passphrase
linstor resource-definition create crypt_rsc --layer-list luks,storage
#To enter the master passphrase (after controller restart) use the following command:
linstor encryption enter-passphrase
```
### Automatic Passphrase
```bash
/etc/linstor/linstor.toml
[encrypt]
passphrase="example"
```

