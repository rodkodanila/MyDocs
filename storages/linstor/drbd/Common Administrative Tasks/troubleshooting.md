# Troubleshooting and Error Recovery
## Getting Information About DRBD Error Codes
```bash
perl -e 'print $! = 11, "\n"'
```

# Manually Detaching DRBD from Your Hard Disk
If DRBD is configured to pass on I/O errors (not recommended), you must first detach the DRBD resource, that is, disassociate it from its backing storage:
```bash
drbdadm detach <resource>
drbdadm status <resource>
```

# Replacing a Failed Disk When Using Internal Metadata
```bash
drbdadm create-md <resource>
drbdadm attach <resource>
```
#  Replacing a Failed Disk When Using External Metadata
```bash
drbdadm create-md <resource>
drbdadm attach <resource>
drbdadm invalidate <resource>
```

#  Manual Split Brain Recovery
```bash
drbdadm disconnect <resource>
drbdadm secondary <resource>
drbdadm connect --discard-my-data <resource>
```
# on surviver split brain node
```bash
drbdadm disconnect <resource>
drbdadm connect <resource>
```

# Recovering a Primary Node that Lost Quorum
The DRBD administration tool, drbdadm, includes a force secondary option, secondary --force. If DRBD quorum was configured to suspend DRBD resource I/O operations upon loss of quorum, the force secondary option will allow you to gracefully recover the node that lost quorum and reintegrate it with the other nodes.

```bash
drbdadm secondary --force <all|resource_name>
```
