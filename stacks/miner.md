Stacks node requires a Bitcoin node to connect to on the back-end for [Proof of Transfer](https://docs.stacks.co/understand-stacks/proof-of-transfer). You can deploy a Bitcoin node using Kotal operator like we did in this [guide](../bitcoin/rpc.md). In this tutorial, we will be using Bitcoin node hosted by Stacks Foundation.

{% hint style="warning" %}
Make sure Bitcoin node is fully synced before deploying your Stacks node.
{% endhint %}

## Generating Miner Private Key

{% hint style="warning" %}
Don't use the following Miner private key in production!
{% endhint %}

Generate Key chain using [Stacks CLI](https://github.com/hirosystems/stacks.js/tree/master/packages/cli) tool. Stacks CLI can be installed using:

```bash
npm install -g @stacks/cli
```

Generate key chain using:

```bash
stx make_keychain
```

It will return an output similar to the following

```json
{
  "mnemonic": "blue news bid also tell vault blame tonight crumble history tribe anxiety arch stove usage eight stick firm weapon wet chapter gravity seat idle",
  "keyInfo": {
    "privateKey": "7a3ef70a4a3ff3c389818ac11abec24b20ffbe3fb2cf6f71e947367a4ddbec6601",
    "address": "SPQG93WFTVV6GA4AEDMS18ZCSTKMKFHWEHEXQAJJ",
    "btcAddress": "15HcDuW1W2TqEe9mVER9ceJYtqMAED26jp",
    "wif": "L1KLjY5xMGUSSV9cfE9VoKRdVt3D1zJxoJwPVLHfgRNS1ETGUSvr",
    "index": 0
  }
}
```

Store `privateKey` from the output above into a Kubernetes secret in data field called `key`:

```
kubectl create secret generic seed-private-key --from-literal=key=7a3ef70a4a3ff3c389818ac11abec24b20ffbe3fb2cf6f71e947367a4ddbec6601
```

## Bitcoin node JSON-RPC user password

Let's store Stacks Foundation Bitcoin node JSON-RPC password in a Kubernetes secret to be used by our node:

{% code title="bitcoin-node-rpc-password.yaml" %}
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: bitcoin-node-rpc-password
stringData:
  password: blockstacksystem
```
{% endcode %}

Apply `bitcoin-node-rpc-password.yaml` to create the password secret:

```bash
kubectl apply -f bitcoin-node-rpc-password.yaml
```

## Deploy Stacks Miner Node

The following manifest describes a Stacks node that syncs Stacks mainnet `network: mainnet`, and connects to the bitcoin node using configurations in `bitcoinNode: ...` for Proof of Transfer, and loading miner private key using `seedPrivateKeySecretName`:

{% code title="stacks.yaml" %}
```yaml
apiVersion: stacks.kotal.io/v1alpha1
kind: Node
metadata:
  name: stacks-node
spec:
  network: mainnet
  seedPrivateKeySecretName: seed-private-key
  bitcoinNode:
    endpoint: bitcoin.blockstack.com
    rpcPort: 8332
    p2pPort: 8333
    rpcUsername: blockstack
    rpcPasswordSecretName: bitcoin-node-rpc-password
```
{% endcode %}

Apply `stacks.yaml` manifest:

```bash
kubectl apply -f stacks.yaml
```

Kotal operator will notice your `stacks-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets neccessary.

You can fetch the deployed Stacks `Node` using:

```bash
kubectl get nodes.stacks
```

It will return an output similar to the following:

```bash
NAME            NETWORK    CLIENT    MINER
stacks-node     mainnet    stacks    true
```

Note Miner is true in the previous output 🔥

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME             READY   STATUS    RESTARTS   AGE
stacks-node-0    1/1     Running   0          1m
```

Get the logs of the running node:

```bash
kubectl logs -f stacks-node-0
```

It will return node logs similar to the following:

```bash
INFO [1649610683.233993] [testnet/stacks-node/src/main.rs:113] [main] Loading config at path /home/stacks/kotal-config/config.toml
INFO [1649610683.795606] [testnet/stacks-node/src/run_loop/neon.rs:341] [main] Start syncing Bitcoin headers, feel free to grab a cup of coffee, this can take a while
INFO [1649610686.606279] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 0.3% (2000 out of 731294)
INFO [1649610687.625014] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 0.5% (4000 out of 731294)
INFO [1649610688.634560] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 0.8% (6000 out of 731294)
INFO [1649610689.677533] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.1% (8000 out of 731294)
INFO [1649610690.737928] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.4% (10000 out of 731294)
INFO [1649610691.745002] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.6% (12000 out of 731294)
INFO [1649610692.789689] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.9% (14000 out of 731294)
...
```

Finally you can delete the node by:

```bash
kubectl delete -f stacks.yaml
```

Kubernetes garbage collector will delete all the resources that has been created by Stacks `Node` controller.
