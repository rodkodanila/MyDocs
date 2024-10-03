# Evacuating a Node
#### Node evacuate to evacuate a node of its resources, for example, if you are preparing to delete a node from your cluster, and you need the node’s resources moved to other nodes in the cluster. After successfully evacuating a node, the node’s LINSTOR status will show as “EVACUATE” rather than “Online”, and it will have no LINSTOR resources on it.

```text
Evacuate a node using the following steps:
1. None of the resources on the node should be “InUse”
2. linstor node evacuate <node_name>
3. linstor node list - verify that status evacuated
4. linstor resource list --nodes <node_name>
5. linstor node delete <node_name>
```

# Evacuating Multiple Nodes
```bash
linstor node set-property <node_name> AutoplaceTarget false
```

# Restoring an Evacuating Node
```bash
linstor node restore <nodename> 
node set-property <node_name> AutoplaceTarget true
```

