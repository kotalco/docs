We've extended Kubernetes with `Node` custom resource which can be used to deploy Ethereum nodes from the given spec.

```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: my-node
spec:
  # your node spec goes here
```

Using Ethereum `Node` custom resource, you can describe any public or private network node you want, and Kotal will create all the necessary Kubernetes resources like pods, persistent volumes, services, configmaps, and secrets for you.

If you want to get all the deployted ethereum `Node`(s):

```bash
$ kubectl get nodes.ethereum
```

Which will report node name, client, network, and consensus.

If you want to get all the fields associated with ethereum `Node`:

```bash
$ kubectl explain nodes.ethereum
```

For a comprehensive reference on Ethereum `Node`, check our [ethereum reference](../reference/ethereum.md)

## Multi-client Support

{% hint style="warning" %}
OpenEthereum client will be deprecated after the London upgrade. The team is working with Erigon on a smooth transition path for users. More information can be found [here](https://medium.com/openethereum/gnosis-joins-erigon-formerly-turbo-geth-to-release-next-gen-ethereum-client-c6708dd06dd)
{% endhint %}

Kotal supports the following Ethereum 1 clients:

* [Hyperledger Besu](https://besu.hyperledger.org)
* [Go-Ethereum (Geth)](https://geth.ethereum.org)
* [Nethermind](https://nethermind.io)
* [Parity (OpenEthereum)](https://www.parity.io/ethereum/).


