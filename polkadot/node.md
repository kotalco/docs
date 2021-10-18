## Deploy Polkadot Node

The following manifest describes a Polkadot node that syncs Polkadot relay chain `network: polkadot`, enables HTTP JSON-RPC server `rpc: true` on custom port `rpcPort: 8888`, and uses [Parity Polkadot](https://github.com/paritytech/polkadot) client:

{% code title="polkadot.yaml" %}
```yaml
apiVersion: polkadot.kotal.io/v1alpha1
kind: Node
metadata:
  name: polkadot-node
spec:
  network: polkadot
  rpc: true
  rpcPort: 8888
```
{% endcode %}

Apply `polkadot.yaml` manifest:

```bash
kubectl apply -f polkadot.yaml
```

Kotal operator will notice your `polkadot-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets neccessary.

You can fetch the deployed Polkadot `Node` using:

```bash
kubectl get nodes.polkadot
```
It will return an output similar to the following:

```bash
NAME             NETWORK     VALIDATOR
polkadot-node    polkadot    fasle
```

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME                  READY   STATUS    RESTARTS   AGE
polkadot-node-0       1/1     Running   0          1m
```

Get the logs of the running node:

```bash
kubectl logs -f polkadot-node-0
```

It will return node logs similar to the following:

```
2021-10-17 22:15:12 Parity Polkadot
2021-10-17 22:15:12 ✌️  version 0.9.11-bfd38ed62-x86_64-linux-gnu
2021-10-17 22:15:12 ❤️  by Parity Technologies <admin@parity.io>, 2017-2021
2021-10-17 22:15:12 📋 Chain specification: Polkadot
2021-10-17 22:15:12 🏷 Node name: polkadot-node
2021-10-17 22:15:12 👤 Role: FULL
2021-10-17 22:15:12 💾 Database: RocksDb at /polkadot/kotal-data/chains/polkadot/db/full
2021-10-17 22:15:12 ⛓  Native runtime: polkadot-9110 (parity-polkadot-0.tx8.au0)
2021-10-17 22:15:12 🔨 Initializing Genesis block/state (state: 0x29d0…4e17, header-hash: 0x91b1…90c3)
2021-10-17 22:15:12 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.
2021-10-17 22:15:13 ⏱  Loaded block-time = 6s from block 0x91b171bb158e2d3848fa23a9f1c25182fb8e20313b2c1eb49219da7a70ce90c3
2021-10-17 22:15:13 👶 Creating empty BABE epoch changes on what appears to be first startup.
2021-10-17 22:15:13 🏷 Local node identity is: 12D3KooWRo3vvfgPdsJ44tXu1Y2GeFNmC9ror2HsdJjvSjEcfZdw
2021-10-17 22:15:13 📦 Highest known block at #0
2021-10-17 22:15:13 Listening for new connections on 127.0.0.1:9944.
2021-10-17 22:15:13 🔍 Discovered new external address for our node: /ip4/102.189.29.174/tcp/30333/ws/p2p/12D3KooWRo3vvfgPdsJ44tXu1Y2GeFNmC9ror2HsdJjvSjEcfZdw
2021-10-17 22:15:18 ⚙️  Syncing, target=#7304424 (1 peers), best: #64 (0x4983…1114), finalized #0 (0x91b1…90c3), ⬇ 37.0kiB/s ⬆ 17.6kiB/s
```

## Call JSON-RPC Method

Forward localhost:8888 calls to the node pod because `rpcPort: 8888`:

```bash
kubectl port-forward polkadot-node-0 8888
```

In another terminal window call `eth_getBlock` JSON-RPC method to get the latest synced block:

```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "chain_getBlock", "params": []}' 0.0.0.0:8888
```

You will get JSON result similar to the following:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "block": {
      "extrinsics": [
        "0x280403000be06ab2527201",
        "0x14040a00912e",
        "0x1004140000"
      ],
      "header": {
        "digest": {
          "logs": [
            "0x0642414245b501030000000042eacc0f00000000f414617b1dbce8671e029af90ae45c4b51d2f8d77d7876ae5f19dd42b7cb213775f40642dc2d86dd0a16169008e2d8735c849d7113da468be33b1c85819310024daffbee165156a2c17919fdc592754960a5587b6affc81f3e9c260364da2200",
            "0x054241424501019243678a110c1271c784ba9abaf03e08ae635ea766ce953853bfb1e3c0f70b5c95907240de74e57aa379c8428cd9933f097e32f9ef82caf261277ba4dc573b8b"
          ]
        },
        "extrinsicsRoot": "0xd9c14a96f36005ef6487ff56cb4722ff64afa3535cb1464c988767084094566c",
        "number": "0xba7",
        "parentHash": "0xcfb74aa6750036482897f63806191b3b14447186e45f69c43fcb72e02b57871f",
        "stateRoot": "0x5116aa11f10b1b1ce7c05ea85de08238b7db4c2c485bc88a9167e3b0a153368b"
      }
    },
    "justifications": null
  },
  "id": 1
}
```

Finally you can delete the node by:

```bash
kubectl delete -f polkadot.yaml

node.polkadot.kotal.io "polkadot-node" deleted
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal Polkadot `Node` controller.