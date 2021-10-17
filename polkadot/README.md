Polkadot is a sharded protocol that enables blockchain networks to operate together seamlessly. Polkadot is a 100% open-source project created to enable a decentralized web and better society.

We've extended Kubernetes with `Node` custom resource which can be used to Polkadot, Kusama, Rococo, and substrate-based chains from the given spec.

## Node

`Node` is Polkadot node using [parity polkadot](https://github.com/paritytech/polkadot) client, connecting to and syncing a specific chain, and optionally validating blocks.

```yaml
apiVersion: polkadot.kotal.io/v1alpha1
kind: Node
metadata:
  Name: kusama-node
spec:
  network: kusama
  rpc: true
```

For all the fields associated with the `Node` API resource:

```bash
kubectl explain nodes --api-version polkadot.kotal.io/v1alpha1
kubectl explain nodes.spec --api-version polkadot.kotal.io/v1alpha1
```

Full polkadot node reference is documented [here](../reference/polkadot.md).
