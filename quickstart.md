Create `rinkeby.yaml` manifest that describes an Ethereum node which uses `go-ethereum` client, connecting to rinkeby network, and enabling JSON-RPC server:

{% code title="rinkeby.yaml" %}
```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: rinkeby-geth-node
spec:
  network: rinkeby
  client: geth
  rpc: true
```
{% endcode %}

Deploy the node using kubectl:

```bash
$ kubectl apply -f rinkeby.yaml

node.ethereum.kotal.io/rinkeby-geth-node created
```

Within a couple of seconds, the node will be up and running. You can get the node using:

```bash
$ kubectl get nodes.ethereum

NAME                 CLIENT   Network   Consensus
rinkeby-geth-node    geth     rinkeby   poa
```

Kotal will create all the necessary pods, volumes, services for the node. Get the pods by:

```bash
$ kubectl get pods

NAME                     READY   STATUS    RESTARTS   AGE
rinkeby-geth-node-0      1/1     Running   0          1m
```

Finally delete the node by:

```bash
$ kubectl delete -f rinkeby.yaml

node.ethereum.kotal.io "rinkeby-geth-node" deleted
```

Congratulations!