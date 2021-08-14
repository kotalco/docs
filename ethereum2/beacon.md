> The Beacon Chain is the coordination mechanism of the new network, responsible for creating new blocks, making sure those new blocks are valid, and rewarding validators with ETH for keeping the network secure.
> [Source](https://consensys.net/blog/blockchain-explained/the-ethereum-2-0-beacon-chain-is-here-now-what/)

## Deploy Beacon Node

{% code title="beacon.yaml" %}
```yaml
apiVersion: ethereum2.kotal.io/v1alpha1
kind: BeaconNode
metadata:
  name: teku-beacon-node
spec:
  network: pyrmont
  client: teku
  rest: true
  restPort: 8888
  eth1Endpoints:
    - http://goerli-besu-node:8545
```
{% endcode %}

In this beacon node, we're using ConsenSys Teku Ethereum 2.0 client `client: teku`, and syncing the pyrmont network beacon chain `network: pyrmont`. We also enable REST API server `rest: true` at port 8888 `restPort: 8888`. We're connecting to Ethereum 1 endpoint `eth1Endpoints: ...`. Check Ethereum [tutorial](../ethereum/README.md) on how to deploy an Ethereum node.

Let's deploy the beacon node:

```bash
kubectl apply -f beacon.yaml
```

Kotal operator will notice your `teku-beacon-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets.

You can fetch the deployed Ethereum 2.0 `BeaconNode` using:

```bash
kubectl get beaconnodes
```

It will return an output similar to the following:

```bash
NAME               CLIENT   Network   AGE
teku-beacon-node   teku     pyrmont   1m
```

## Fetch Beacon Node Logs

Get the pods created for the beacon node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME                 READY   STATUS    RESTARTS   AGE
teku-beacon-node-0   1/1     Running   0          1m
```

Get the logs of the running beacon node:

```bash
kubectl logs -f teku-beacon-node-0
```

## Call `/eth/v1/beacon/genesis` REST API

Get the service created for the beacon node:

```bash
kubectl get services
```

It will return an output similar to the following:

```
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
teku-beacon-node   ClusterIP   10.96.197.218   <none>        9000/UDP,9000/TCP,8888/TCP   7s
```

Forward the localhost:8888 calls to the beacon node

```bash
kubectl port-forward teku-beacon-node-0 8888
```

In another terminal window, send REST API call to get chain genesis details:

```bash
curl localhost:8888/eth/v1/beacon/genesis
```

You'll get a result similar to the following:

```json
{
  "data": {
    "genesis_time": "1606824023",
    "genesis_validators_root": "0x4b363db94e286120d76eb905340fdd4e54bfe9f06bf33ff6cf5ad27f511bfe95",
    "genesis_fork_version": "0x00000000"
  }
}
```

Finally delete the beacon node and kubernetes will delete all resources created for the beacon node like pods, services ... etc:

```bash
kubectl delete beaconnode teku-beacon-node
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal Ethereum 2.0 `BeaconNode` controller.

