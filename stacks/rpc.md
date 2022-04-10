Stacks node requires a Bitcoin node to connect to on the back-end for [Proof of Transfer](https://docs.stacks.co/understand-stacks/proof-of-transfer). You can deploy a Bitcoin node using Kotal operator like we did in this [guide](../bitcoin/rpc.md). In this tutorial, we will be using Bitcoin node hosted by Stacks Foundation.

{% hint style="warning" %}
Make sure Bitcoin node is fully synced before deploying your Stacks node.
{% endhint %}

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

## Deploy Stacks RPC Node

The following manifest describes a Stacks node that syncs Stacks mainnet `network: mainnet`, and connects to the bitcoin node using configurations in `bitcoinNode: ...` for Proof of Transfer:

{% code title="stacks.yaml" %}
```yaml
apiVersion: stacks.kotal.io/v1alpha1
kind: Node
metadata:
  name: stacks-node
spec:
  network: mainnet
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
stacks-node     mainnet    stacks    false
```

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
INFO [1649597250.010714] [testnet/stacks-node/src/main.rs:113] [main] Loading config at path /home/stacks/kotal-config/config.toml
INFO [1649597250.599067] [testnet/stacks-node/src/run_loop/neon.rs:341] [main] Start syncing Bitcoin headers, feel free to grab a cup of coffee, this can take a while
INFO [1649597251.949664] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 0.3% (2000 out of 731268)
INFO [1649597253.010039] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 0.5% (4000 out of 731268)
INFO [1649597254.019798] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 0.8% (6000 out of 731268)
INFO [1649597254.992070] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.1% (8000 out of 731268)
INFO [1649597256.214756] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.4% (10000 out of 731268)
INFO [1649597257.276950] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.6% (12000 out of 731268)
INFO [1649597258.383248] [src/burnchains/bitcoin/spv.rs:923] [main] Syncing Bitcoin headers: 1.9% (14000 out of 731268)
...
```

## Call JSON-RPC Method

By default, Stacks node JSON-RPC server port is `20443`, which can be changed using `rpcPort`.

Forward localhost:20443 calls to the node pod:

```bash
kubectl port-forward bitcoin-node-0 20443
```

{% hint style="warning" %}
HTTP server will start after Bitcoin headers sync, till then you will get the following error if you tried to send JSON-RPC calls:
```bash
curl: (52) Empty reply from server
```
{% endhint %}

In another terminal window call `info` JSON-RPC method to retrieve information about the Core API including the server version:

```bash
curl localhost:20443/v2/info
```

You will get JSON result similar to the following:

```json
{
  "peer_version": 402653189,
  "pox_consensus": "9bed78c9f02d2ba5a5950fe88c4746436b47c730",
  "burn_block_height": 666051,
  "stable_pox_consensus": "0000000000000000000000000000000000000000",
  "stable_burn_block_height": 666050,
  "server_version": "stacks-node No Version Info (No Branch Info:No Commit Info, release build, linux [x86_64])",
  "network_id": 1,
  "parent_network_id": 3652501241,
  "stacks_tip_height": 0,
  "stacks_tip": "0000000000000000000000000000000000000000000000000000000000000000",
  "stacks_tip_consensus_hash": "0000000000000000000000000000000000000000",
  "genesis_chainstate_hash": "74237aa39aa50a83de11a4f53e9d3bb7d43461d1de9873f402e5453ae60bc59b",
  "unanchored_tip": null,
  "unanchored_seq": null,
  "exit_at_block_height": null
}
```


Finally you can delete the node by:

```bash
kubectl delete -f stacks.yaml
```

Kubernetes garbage collector will delete all the resources that has been created by Stacks `Node` controller.
