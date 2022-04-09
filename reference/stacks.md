## Node

| Syntax                                                | Type    | Description                                                     | Default |
| ----------------------------------------------------- | ------- | --------------------------------------------------------------- | ------- |
| [network](#network) <sup>required</sup>               | string  | Stacks network to join and sync                                 |         |
| [rpcPort](#rpcport)                                   | number  | JSON-RPC server port                                            | `20443` |
| [rpcHost](#rpchost)                                   | string  | JSON-RPC server host                                            | 0.0.0.0 |
| [p2pPort](#p2pport)                                   | number  | p2p bind port                                                   | `20444` |
| [p2pHost](#p2phost)                                   | string  | p2p bind host                                                   | 0.0.0.0 |
| [bitcoinNode](#bitcoinnode)                           | object  | Bitcoin node details                                            |         |
| [miner](#miner)                                       | boolean | Enables mining                                                  | false   |
| [seedPrivateKeySecretName](#seedprivatekeysecretname) | string  | Kubernetes secret name holding seed private key used for mining |         |
| [mineMicroblocks](#minemicroblocks)                   | boolean | Mines Stacks micro blocks                                       | false   |
| [nodePrivateKeySecretName](#nodeprivatekeysecretname) | string  | Kubernetes secret name holding node private key                 |         |
| [resources](#resources)                               | object  | node compute and storage resources to alloacte                  |         |

### network

`network` is Stacks network to join and sync. Possible values are `mainnet` and `testnet`.

`network` is immutable, can't be changed after node is created.

### rpcPort

`rpcPort` is JSON-RPC server port.

### rpcHost

`rpcHost` is JSON-RPC server host.

### p2pPort

`p2pPort` is p2p bind port.

### p2pHost

`p2pHost` is p2p bind host.

### bitcoinNode

`bitcoinNode` is Bitcoin node details for Stacks node to connect and query:

| Syntax                | Type   | Description                                                   |
| --------------------- | ------ | ------------------------------------------------------------- |
| endpoint              | string | Stacks network to join and sync                               |
| p2pPort               | number | p2p bind port                                                 |
| rpcPort               | number | JSON-RPC server host                                          |
| rpcUsername           | string | Bitcoin node JSON-RPC username                                |
| rpcPasswordSecretName | string | Kubernetes secret name holding bitcoin node JSON-RPC password |


### miner

`miner` enables mining.

### seedPrivateKeySecretName

`seedPrivateKeySecretName` is Kubernetes secret name holding seed private key used for mining.

### mineMicroblocks

`mineMicroblocks` enables mining Stacks micro blocks.

### nodePrivateKeySecretName

`nodePrivateKeySecretName` is Kubernetes secret name holding node private key.

### resources

`resources` allocates compute and storage resources to the node.

| Syntax       | Type   | Description                                 | Default                                                                                    |
| ------------ | ------ | ------------------------------------------- | ------------------------------------------------------------------------------------------ |
| cpu          | string | number of cpu cores this node requires      | `2`                                                                                        |
| cpuLimit     | string | number of cpu cores this node is limited to | `4`                                                                                        |
| memory       | string | memory this node requires                   | `4Gi`                                                                                      |
| memoryLimit  | string | memory this node is limited to              | `8Gi`                                                                                      |
| storage      | string | disk space this node requires               | `100Gi`                                                                                    |
| storageClass | string | Node volume storage class                   | Cluster's default storage class will be used as defined by cluster admin or cloud provider |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1`), `1500m`, `2`, and `4.`

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than or equal to `memory`.

`storageClass` field is immutable, it cannot be changed after creation.
