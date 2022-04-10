
We've extended Kubernetes with `Node` custom resource which can be used to deploy Stacks rpc and miner nodes from the given spec.

## Node

`Node` is Stacks node using [Stacks Blockchain Node](https://github.com/stacks-network/stacks-blockchain) client, connecting to and syncing a specific chain, optionally mining or exposing JSON-RPC server to be used by clients.

```yaml
apiVersion: stacks.kotal.io/v1alpha1
kind: Node
metadata:
  Name: stacks-node
spec:
  network: mainnet
  ...
```

For all the fields associated with the `Node` API resource:

```bash
kubectl explain nodes --api-version stacks.kotal.io/v1alpha1
kubectl explain nodes.spec --api-version stacks.kotal.io/v1alpha1
```

Full Stacks node reference is documented [here](../reference/stacks.md).
