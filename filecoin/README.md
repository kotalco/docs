We've extended Kubernetes with `Node` custom resource which can be used to create Filecoin nodes across different filecoin networks like Mainnet or Nerpa using `lotus` client from a given spec.

Here's an example of a Filecoin node that syncs Nerpa chain:

```yaml
apiVersion: filecoin.kotal.io/v1alpha1
kind: Node
metadata:
  name: nerpa-node
spec:
  network: nerpa
```

For all the fields associated with the `Node` API resource:

```bash
kubectl explain node --api-version filecoin.kotal.io/v1alpha1
kubectl explain node.spec --api-version filecoin.kotal.io/v1alpha1
```

Full filecoin node reference is documented [here](../reference/filecoin.md).