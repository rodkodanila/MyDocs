# Configure DRBD 
# 1. main config 
/etc/drbd.conf

#  Simple DRBD configuration
```text
global {
  usage-count yes;
}
common {
  net {
    protocol C;
  }
}
```

# Simple DRBD resource configuration (/etc/drbd.d/r0.res)
```bash
cat << EOF > /etc/drbd.d/r0.res
resource "r0" {
  device minor 1;
  disk "/dev/sdb";
  meta-disk internal;

  on "linstor1" {
    node-id 0;
  }
  on "linstor2" {
    node-id 1;
  }
  connection {
    host "linstor1" address 192.168.138.251:7789;
    host "linstor2" address 192.168.138.250:7789;
  }
}
EOF
```

# Multi volume example 
```text
resource "r0" {
  volume 0 {
    device minor 1;
    disk "/dev/sda7";
    meta-disk internal;
  }
  volume 1 {
    device minor 2;
    disk "/dev/sda8";
    meta-disk internal;
  }
  on "alice" {
    node-id 0;
  }
  on "bob" {
    node-id 1;
    volume 1 {
      disk "/dev/sda9";
    }
  }
  connection {
    host "alice" address 10.1.1.31:7789;
    host "bob" address 10.1.1.32:7789;
  }
}
```

# for 3 hosts

```bash
cat << EOF > /etc/drbd.d/r0.res
resource r1 {
  device    minor 1;
  disk      "/dev/sdb";
  meta-disk internal;
  on linstor1 {
    address   192.168.138.251:7000;
    node-id   0;
  }
  on linstor2 {
    address   192.168.138.250:7000;
    node-id   1;
  }
  on linstor3 {
    address   192.168.138.252:7000;
    node-id   2;
  }
  connection-mesh {
    hosts linstor1 linstor2 linstor3;
  }
}
EOF
```

# 2. Enabling your resource for the first time
```bash
drbdadm create-md r1
```
# 3. Up resource
```bash
drbdadm up r1
```

# 4. Check Status
```bash
drbdadm status r1
```

# 5. Start the initial full synchronization (transfers 0 blocks)
```bash
drbdadm primary --force r1
```

# 6. We can skip it if we have clear disks
```bash
drbdadm create-md <res>
drbdadm up <res>
# exec in one node
drbdadm new-current-uuid --clear-bitmap <resource>/<volume>
# or
drbdsetup new-current-uuid --clear-bitmap <minor>
```
