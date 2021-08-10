## Deploy Private Network Node

{% hint style="danger" %}

In this tutorial we will use the following private keys and their corresponding addresses. 

**DON'T** use these keys in production.

| Address                                    | Private Key                                                      |
| ------------------------------------------ | ---------------------------------------------------------------- |
| 0xbAa5f05af4A67A467cEcA89085f162aFb4206Aaa | fb5411342ae51291447515c89bcf6a057e3dbd0b51e060c45cb73406c38f851d |
| 0xc1381ED43B327e3C7A1ADb21285f1e9cB82Bc00d | 153b174f5e9948ae4678baed54f88244cc9c39d56b9f17ecef93d7ede633f56b |
| 0x7DE985E2f878c83C4e91b6B1312c0f63A56C844a | 89b03c4de62d61be16d22e09c8a48929a9bccd11fa6b37809cfef290292bcba3 |

{% endhint %}

Let's describe an Ethereum `Node` that uses a custom genesis block to join a private Proof of Authority network using **Clique** consensus configuration in the genesis block.

{% code title="besu-clique-node.yaml" %}
```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: besu-clique-node
spec:
  client: besu
  nodePrivatekeySecretName: besu-clique-nodekey
  rpc: true
  genesis:
    chainId: 4444
    networkId: 4444
    clique:
      signers:
        - "0xbAa5f05af4A67A467cEcA89085f162aFb4206Aaa"
        - "0xc1381ED43B327e3C7A1ADb21285f1e9cB82Bc00d"
        - "0x7DE985E2f878c83C4e91b6B1312c0f63A56C844a"
```
{% endcode %}

In this node, we're using Hyperledger besu client `client: besu`, enabling JSON-RPC server `rpc: true` so we can query number of peers later in this tutorial, and we're loading the node private key from Kubernetes secretd called `besu-clique-nodekey`.

The node private key will give the node a unique identity and node URL, and will allow the node to generate blocks, because the address `0xbAa5f05af4A67A467cEcA89085f162aFb4206Aaa` that's corresponding to the node private key is in the initial block signers.

We're defining a genesis block that uses the value `4444` as network and chain identifier, and we start the chain with 3 signers as defined by `spec.genesis.clique.signers`.

This node private key secret can be created by:

```bash
kubectl create secret generic besu-clique-nodekey --from-literal=key=fb5411342ae51291447515c89bcf6a057e3dbd0b51e060c45cb73406c38f851d
```

{% hint style="info" %}
Private key must **not** start with `0x`, and must be stored in secret data field called `key`.
{% endhint %}

Let's deploy the node:

```bash
kubectl apply -f besu-clique-node.yaml
```

Kotal operator will notice your `besu-clique-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets.

You can fetch the deployed Ethereum `Node` using:

```bash
kubectl get nodes.ethereum
```
It will return an output similar to the following:

```bash
NAME                 CLIENT   Consensus   Network
besu-clique-node     besu     poa         private
```

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```
It will return an output similar to the following:

```bash
NAME                  READY   STATUS    RESTARTS   AGE
besu-clique-node-0    1/1     Running   0          1m
```

Get the logs of the running node:

```bash
kubectl logs -f besu-clique-node-0
```

## Deploy a Second Node

Let's deploy another go-ethereum node, and connect it to the previous node in our private proof of authority network.

{% hint style="info" %}
Genesis block must be the same in both nodes, or they will fork at genesis block, and won't reach consensus.
{% endhint %}

{% code title="geth-clique-node.yaml" %}
```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: geth-clique-node
spec:
  client: geth
  miner: true
  coinbase: "0xc1381ED43B327e3C7A1ADb21285f1e9cB82Bc00d"
  import:
    privatekeySecretName: geth-clique-account-key
    passwordSecretName: geth-clique-account-password
  staticNodes:
    - besu-clique-node
  genesis:
    chainId: 4444
    networkId: 4444
    clique:
      signers:
        - "0xbAa5f05af4A67A467cEcA89085f162aFb4206Aaa"
        - "0xc1381ED43B327e3C7A1ADb21285f1e9cB82Bc00d"
        - "0x7DE985E2f878c83C4e91b6B1312c0f63A56C844a"
```
{% endcode %}

In this node, we're using go-ethereum client `client: geth`, starting the PoA consensus engine `miner: true`, setting the second address in the genesis signers list `spec.genesis.clique.signers` as the coinbase `coinbase: "0xc1381ED43B327e3C7A1ADb21285f1e9cB82Bc00d"`, and loading the signer account private key and password from kubernetes secrets `privatekeySecretName: ...` and `passwordSecretName: ...`. We're connecting to the first node using `staticNodes` option which accepts `Node` name or enode url.

{% hint style="info" %}
`staticNodes` accept `Node` name or enode URL. `Node` name has the format of `name.namespace`, namespace is optional if `Node` is in the same namespace. If the node doesn't exist, or is not up and running yet, Kotal will not raise an error.
{% endhint %}

You can create the private key and password secrets using:

```bash
kubectl create secret generic geth-clique-account-key --from-literal=key=153b174f5e9948ae4678baed54f88244cc9c39d56b9f17ecef93d7ede633f56b
kubectl create secret generic geth-clique-account-password --from-literal=password=s3cr3t
```

Deploy the second node using:

```bash
kubectl apply -f geth-clique-node.yaml
```

Kotal operator will notice your second `geth-clique-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets.

You can fetch the deployed Ethereum `Node`s using:

```bash
kubectl get nodes.ethereum
```

It will return an output similar to the following:

```bash
NAME                 CLIENT   Consensus   Network
besu-clique-node     besu     poa         private
geth-clique-node     geth     poa         private
```

## Call JSON-RPC Method `net_peerCount`

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME                  READY   STATUS    RESTARTS   AGE
besu-clique-node-0    1/1     Running   0          1m
geth-clique-node-0    1/1     Running   0          1m
```

Forward localhost:8545 calls to the node pod:

```bash
kubectl port-forward besu-clique-node-0 8545
```

In another terminal window call `net_peerCount` JSON-RPC method

```bash
curl -X POST -H 'content-type: application/json' --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":32}' http://127.0.0.1:8545
```

You will get JSON result similar to the following:

```JSON
{
  "jsonrpc" : "2.0",
  "id" : 32,
  "result" : "0x1"
}
```

## Homework

Deploy a third node that uses Nethermind client, and signing blocks using the third key in the signers list `spec.genesis.clique.signers`. Nethermind client is similar to geth, you will import signer account private key and password from kubernetes secrets, and use the same genesis as the other nodes.


Finally you can delete all the nodes by:

```bash
kubectl delete nodes.ethereum --all
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal Ethereum `Node` controller.

