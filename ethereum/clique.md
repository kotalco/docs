Using Ethereum `Node` custom resource, you can deploy a node that joins a public network by setting `spec.network` parameter, or setting `spec.genesis` to start or join private network.

In this example, we will create private Proof of Authority network using Clique consensus algorithm.

```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: my-node
spec:
  network: # public network to join
  genesis: # private network genesis
  # can't set both .network and .genesis
```

## Deploy Private Network Node


## Deploy a Second Node

## Call JSON-RPC Method `net_peerCount`

