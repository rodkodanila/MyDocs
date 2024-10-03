# Online Device Verification
/etc/drbd.conf
```text
resource <resource>
  net {
    verify-alg <algorithm>;
  }
  ...
}
```
# Invoking Online Verification
```bash
drbdadm verify <resource>:<peer>/<volume>
```

# automating verification
```crontab
42 0 * * 0    root    /sbin/drbdadm verify <resource>(or all)
```
# Configuring the Rate of Synchronization
```
resource <resource> {
  disk {
    c-plan-ahead 5;
    c-max-rate 10M;
    c-fill-target 2M;
  }
}
```

#  Permanent Fixed Synchronization Rate Configuration
```text
resource <resource>
  disk {
    resync-rate 40M;
    ...
  }
  ...
}
```

#  Configuring Checksum-based Synchronization
```text
resource <resource>
  net {
    csums-alg <algorithm>;
  }
  ...
}
```

# Disabling Backing Device Flushes
```text
resource <resource>
  disk {
    disk-flushes no;
    ...
  }
  ...
}
```

# Data Rebalancing
```text
resource r0 {
  ...
  on for-later-rebalancing {
    address   10.254.254.254:65533;
    node-id   3;
  }
}

drbdadm create-md <resource>
drbdadm adjust <resource>
drbdadm up <resource>
drbdadm status <resource>
```

# Configuring Quorum
```text
resource quorum-demo {
  options {
    quorum <majority|all|numerical value>;
    [...]
  }
  [...]
}
```
