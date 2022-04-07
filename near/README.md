
We've extended Kubernetes with `Node` custom resource which can be used to deploy NEAR nodes from the given spec.

## Node

`Node` is NEAR node using [NEAR Core](https://github.com/near/nearcore) client, connecting to and syncing a specific chain, and optionally validating blocks.

```yaml
apiVersion: near.kotal.io/v1alpha1
kind: Node
metadata:
  Name: near-node
spec:
  network: mainnet
  rpc: true
```

For all the fields associated with the `Node` API resource:

```bash
kubectl explain nodes --api-version near.kotal.io/v1alpha1
kubectl explain nodes.spec --api-version near.kotal.io/v1alpha1
```

Full NEAR node reference is documented [here](../reference/near.md).
