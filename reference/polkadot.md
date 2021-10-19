## Node

| Syntax                                                | Type    | Description                                                        | Default                               |
| ----------------------------------------------------- | ------- | ------------------------------------------------------------------ | ------------------------------------- |
| [network](#network) <sup>required</sup>               | string  | polkadot network/chain to join and sync                            |                                       |
| [p2pPort](#p2pport)                                   | string  | p2p protocol tcp port                                              | 30333                                 |
| [nodePrivateKeySecretName](#nodeprivatekeysecretname) | string  | Kubernetes secret name holding node Ed25519 private key            |                                       |
| [validator](#validator)                               | boolean | enables validator                                                  | false                                 |
| [syncMode](#syncmode)                                 | string  | blockchain synchronization mode                                    | full                                  |
| [pruning](#pruning)                                   | boolean | whether to keep only recent or all blocks                          | false                                 |
| [retainedBlocks](#retainedblocks)                     | number  | number of blocks to keep state for                                 | 256                                   |
| [logging](#logging)                                   | string  | logging verboisty level                                            | info                                  |
| [telemetry](#telemetry)                               | boolean | enables connecting to telemetry server                             | false                                 |
| [telemetryURL](#telemetryurl)                         | string  | telemetry service URL                                              | wss://telemetry.polkadot.io/submit/ 0 |
| [prometheus](#prometheus)                             | string  | exposes prometheus exporter endpoint                               | false                                 |
| [prometheusPort](#prometheusport)                     | number  | prometheus exporter port                                           | 9615                                  |
| [rpc](#rpc)                                           | boolean | enables JSON-RPC server                                            | false                                 |
| [rpcPort](#rpcport)                                   | number  | JSON-RPC server port                                               | 9933                                  |
| [ws](#ws)                                             | boolean | enables Websocket server                                           | false                                 |
| [wsPort](#wsport)                                     | number  | Websocket server port                                              | 9944                                  |
| [corsDomains](#corsdomains)                           | array   | browser origins allowed to access the JSON-RPC HTTP and WS servers | all                                   |
| [resources](#resources)                               | object  | node compute and storage resources to alloacte                     |                                       |

### network

`network` is the polkadot network/chain to join and sync. Possible values are `polkadot`, `kusama`, `rococo`, `westend` and other chains supported by [Parity Polkadot](https://github.com/paritytech/polkadot) client.

### p2pPort

`p2pPort` is p2p protocol tcp port.


### nodePrivateKeySecretName

`nodePrivateKeySecretName` is Kubernetes secret name holding node Ed25519 private key in data field called `key`.

Node private can be generated using [subkey](https://docs.substrate.io/v3/tools/subkey/) tool from Parity Substrate.

```bash
# generate binary node key and save it in node.key file
subkey generate-node-key node.key
# convert node.key binary file into hexadecimal node.key.hex file
xxd -p -c 32 node.key > node.key.hex
```

Create node private key secret:

```bash
kubectl create secret generic polkadot-node-key  --from-file=key=node.key.hex
```

`polkadot-node-key` secret can be used in your polkadot `Node` by updating `.spec` with `nodePrivateKeySecretName: polkadot-node-key`.

### validator

`validator` enables validator.

{% hint style="danger" %}
Node must sync blocks in archive mode `pruning: false` if validator is enabled.
{% endhint %}

### syncMode

`syncMode` is blockchain synchronization mode. Available values are `fast` and `full`.

### pruning

`pruning` controls whether to keep only recent or all blocks. Setting `pruning` to `false` runs the node in archive mode.

### retainedBlocks

`retainedBlocks` is the number of blocks to keep state for. It's only considered if `.spec.pruning` is set to `true`.

### logging

`logging` is logging verboisty level. Available logging levels are `error`, `warn`, `info`, `debug`, and `trace`.


### telemetry

`telemetry` enables connecting to telemetry server.

### telemetryURL

`telemetryURL` is telemetry service URL. It's only considered if `.spec.telemetry` is set to `true`.

### promethems

`prometheus` exposes prometheus exporter endpoint.

### prometheusPort
 
`prometheusPort` is prometheus exporter port

### rpc

`rpc` enables JSON-RPC server.

### rpcPort

`rpcPort` is JSON-RPC server port.

### ws

`ws` enables Websocket server.

### wsPort

`wsPort` is Websocket server port.

### corsDomains

`corsDomains` is browser origins allowed to access the JSON-RPC HTTP and WS servers.

### resources

`resources` allocates compute and storage resources to the node.

| Syntax       | Type   | Description                                 | Default                                                                                    |
| ------------ | ------ | ------------------------------------------- | ------------------------------------------------------------------------------------------ |
| cpu          | string | number of cpu cores this node requires      | `4`                                                                                        |
| cpuLimit     | string | number of cpu cores this node is limited to | `8`                                                                                        |
| memory       | string | memory this node requires                   | `4Gi`                                                                                      |
| memoryLimit  | string | memory this node is limited to              | `8Gi`                                                                                      |
| storage      | string | disk space this node requires               | `80Gi`                                                                                     |
| storageClass | string | Node volume storage class                   | Cluster's default storage class will be used as defined by cluster admin or cloud provider |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1`), `1500m`, `2`, and `4.`

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than or equal to `memory`.

`storageClass` field is immutable, it cannot be changed after creation.
