# Enabling and Disabling Resources
```bash
drbdadm up <resource>
drbdadm down <resource>
```

# Reconfiguring Resources
```test
1.    make any necessary changes to the resource configuration in /etc/drbd.conf,
2.    synchronize your /etc/drbd.conf file between both nodes,
3.    issue the drbdadm adjust <resource> command on both nodes.
```

# Promoting and Demoting Resources
```bash
drbdadm primary <resource>
drbdadm secondary <resource>
```


# Enabling dual-primary mode
```text
resource <resource>
  net {
    protocol C;
    allow-two-primaries yes;
    fencing resource-and-stonith;
  }
  handlers {
    fence-peer "...";
    unfence-peer "...";
  }
  ...
}
```
#  Temporary dual-primary mode
```bash
drbdadm net-options --protocol=C --allow-two-primaries <resource>
```


