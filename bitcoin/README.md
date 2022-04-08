
We've extended Kubernetes with `Node` custom resource which can be used to deploy Bitcoin nodes from the given spec.

## Node

`Node` is Bitcoin node using [Bitcoin Core](https://github.com/bitcoin/bitcoin) client, connecting to and syncing a specific chain.

```yaml
apiVersion: bitcoin.kotal.io/v1alpha1
kind: Node
metadata:
  Name: bitcoin-node
spec:
  network: mainnet
  rpc: true
  ...
```

For all the fields associated with the `Node` API resource:

```bash
kubectl explain nodes --api-version bitcoin.kotal.io/v1alpha1
kubectl explain nodes.spec --api-version bitcoin.kotal.io/v1alpha1
```

Full Bitcoin node reference is documented [here](../reference/bitcoin.md).
