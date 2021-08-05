
{% hint style="warning" %}
Kotal will reject your node if you set both `spec.genesis` and `spec.network`. Use `spec.network` if you're joining a public network, use `spec.genesis` if you're joining private network.
{% endhint %}

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

{% code title="clique.yaml" %}
```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: besu-clique-node
spec:
  client: besu
  nodePrivatekeySecretName: besu-clique-nodekey
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

In this node, we're using Hyperledger besu client `client: besu`, and we're loading the node private key from Kubernetes secretd called `besu-clique-nodekey`.

The node private key will give the node a unique identity and node URL, and will allow the node to generate blocks, because the address `0xbAa5f05af4A67A467cEcA89085f162aFb4206Aaa` that's corresponding to the node private key is in the initial block signers.

We're defining a genesis block that uses the value `4444` as network and chain identifier, and we start the chain with 3 signers as defined by `spec.genesis.clique.signers`.

This node private key secret can be created by:

```bash
$ kubectl create secret generic besu-clique-nodekey --from-literal=key=fb5411342ae51291447515c89bcf6a057e3dbd0b51e060c45cb73406c38f851d

secret/besu-clique-nodekey created
```

{% hint style="info" %}
Private key must **not** start with `0x`, and must be stored in secret data field called `key`.
{% endhint %}

Let's deploy the node:

```bash
$ kubectl apply -f clique.yaml

node.ethereum.kotal.io/besu-clique-node created
```


## Deploy a Second Node

## Call JSON-RPC Method `net_peerCount`

