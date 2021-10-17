## Deploy Rinkeby Node

Rinkeby is a Proof of Authority public Ethereum test network, used by developers to test their dApps.


The following manifest describes an Ethereum node that joins rinkeby network `network: rinkeby`, and uses Hyperledger Besu client `client: besu`:

{% code title="rinkeby.yaml" %}
```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: rinkeby-besu-node
spec:
  network: rinkeby
  client: besu
```
{% endcode %}

{% hint style="info" %}
Joining any Ethereum network is as easy as setting node's `spec.network` value, which accepts values like `mainnet`, `rinkeby`, `goerli`, `xdai` ... etc.
After the node is created, you can't change `spec.network`.
{% endhint %}

Apply `rinkeby.yaml` manifest:

```bash
kubectl apply -f rinkeby.yaml
```

Kotal operator will notice your `rinkeby-besu-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets neccessary.

You can fetch the deployed Ethereum `Node` using:

```bash
kubectl get nodes.ethereum
```
It will return an output similar to the following:

```bash
NAME                 CLIENT   Consensus   Network
rinkeby-besu-node    besu     poa         rinkeby
```

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME                  READY   STATUS    RESTARTS   AGE
rinkeby-besu-node-0   1/1     Running   0          1m
```

{% hint style="info" %}
If pod `STATUS` is `Pending`, most probably it's due to not enough cpu and memory, because Kotal allocates lots of cpu cores and memory for public nodes. The required node resources can be changed using `spec.resources.cpu`, `spec.resources.memory`, and `spec.resources.storage`.
{% endhint %}

Get the logs of the running node:

```bash
kubectl logs -f rinkeby-besu-node-0
```

## Call JSON-RPC Method

Let's update our node by enabling JSON-RPC HTTP server:

{% code title="rinkeby.yaml" %}
```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: rinkeby-besu-node
spec:
  network: rinkeby
  client: besu
  rpc: true
```
{% endcode %}

{% hint style="info" %}

Kotal defaults node missing spec parameters like:

* JSON-RPC HTTP server port is defaulted to 8545.
* JSON-RPC HTTP server enabled modules are defaulted to: eth, net, and web3.
* Blocks Synchronization mode is defauled to `fast` in public networks.

For a comprehensive reference on Ethereum `Node` and default spec parameter values, check our [ethereum reference](../reference/ethereum.md)

{% endhint %}

Apply the new version of `rinkeby.yaml`:

```bash
kubectl apply -f rinkeby.yaml
```

Forward localhost:8545 calls to the node pod:

```bash
kubectl port-forward rinkeby-besu-node-0 8545
```

In another terminal window call `eth_syncing` JSON-RPC method

```bash
curl -X POST -H 'content-type: application/json' --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":32}' http://127.0.0.1:8545
```

You will get JSON result similar to the following:

```json
{
  "jsonrpc" : "2.0",
  "id" : 32,
  "result" : {
    "startingBlock" : "0x0",
    "currentBlock" : "0x1518",
    "highestBlock" : "0x9567a3",
    "pulledStates" : "0x203ca",
    "knownStates" : "0x200636"
  }
}
```

Finally you can delete the node by:

```bash
kubectl delete -f rinkeby.yaml

node.ethereum.kotal.io "rinkeby-besu-node" deleted
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal Ethereum `Node` controller.