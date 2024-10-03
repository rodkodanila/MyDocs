# Monitoring and Performing Actions on DRBD Resources in Real-time
```bash
drbdmon
# or
drbdadm status resource --verbose --statistics
# or 
cat /proc/drbd
# or
drbdsetup events2 --now r0 (realtime without --now)
```
# Connection States
```bash
drbdadm cstate <resource>:<peer>
```

# Check role
```bash
drbdadm role <resource>:peer
```

# Disk states
```bash
drbdadm dstate <resource>:peer
```

# Performance Indicators
```bash
drbdsetup status --verbose --statistics
```


## Disk States
```text
Diskless
No local block device has been assigned to the DRBD driver. This may mean that the resource has never attached to its backing device, that it has been manually detached using drbdadm detach, or that it automatically detached after a lower-level I/O error.

Attaching
Transient state while reading metadata.

Detaching
Transient state while detaching and waiting for ongoing I/O operations to complete.

Failed
Transient state following an I/O failure report by the local block device. Next state: Diskless.

Negotiating
Transient state when an Attach is carried out on an already-Connected DRBD device.

Inconsistent
The data is inconsistent. This status occurs immediately upon creation of a new resource, on both nodes (before the initial full sync). Also, this status is found in one node (the synchronization target) during synchronization.

Outdated
Resource data is consistent, but outdated.

DUnknown
This state is used for the peer disk if no network connection is available.

Consistent
Consistent data of a node without connection. When the connection is established, it is decided whether the data is UpToDate or Outdated.

UpToDate
Consistent, up-to-date state of the data. This is the normal state.

```

# Connection  States

```text

StandAlone
No network configuration available. The resource has not yet been connected, or has been administratively disconnected (using drbdadm disconnect), or has dropped its connection due to failed authentication or split brain.

Disconnecting
Temporary state during disconnection. The next state is StandAlone.

Unconnected
Temporary state, prior to a connection attempt. Possible next states: Connecting.

Timeout
Temporary state following a timeout in the communication with the peer. Next state: Unconnected.

BrokenPipe
Temporary state after the connection to the peer was lost. Next state: Unconnected.

NetworkFailure
Temporary state after the connection to the partner was lost. Next state: Unconnected.

ProtocolError
Temporary state after the connection to the partner was lost. Next state: Unconnected.

TearDown
Temporary state. The peer is closing the connection. Next state: Unconnected.

Connecting
This node is waiting until the peer node becomes visible on the network.

Connected
A DRBD connection has been established, data mirroring is now active. This is the normal state.

```

