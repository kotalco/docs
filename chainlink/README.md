We've extended Kubernetes with `Node` custom resource which can be used to deploy Chainlink nodes from the given spec.

```yaml
apiVersion: chainlink.kotal.io/v1alpha1
kind: Node
metadata:
  name: my-node
spec:
  # your node spec goes here
```

Using Chainlink `Node` custom resource, you can describe any chainlink node, connect to EVM chain node, add jobs and satisfy requests, and Kotal will create all the necessary Kubernetes resources like pods, persistent volumes, services, configmaps, and secrets for you.

If you want to get all the deployted chainlink `Node`(s):

```bash
kubectl get nodes.chainlink
```

Which will report node name, client, Ethereum chain ID, and Link contract address.

If you want to get all the fields associated with chainlink `Node`:

```bash
kubectl explain nodes --api-version chainlink.kotal.io/v1alpha1
```

For a comprehensive reference on Chainlink `Node`, check our [chainlink reference](../reference/chainlink.md)
