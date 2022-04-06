## Node

| Syntax                                                | Type    | Description                                               | Default |
| ----------------------------------------------------- | ------- | --------------------------------------------------------- | ------- |
| [network](#network) <sup>required</sup>               | string  | NEAR network to join and sync                             |         |
| [nodePrivateKeySecretName](#nodeprivatekeysecretname) | string  | Kubernetes secret name holding node Ed25519 private key   |         |
| [validatorSecretName](#validatorsecretname)           | string  | Kubernetes secret name holding node Ed25519 validator key |         |
| [minPeers](#minpeers)                                 | number  | Minimum number of peers to start syncing/producing blocks | 5       |
| [archive](#archive)                                   | boolean | Keeps old blocks in the storage                           | false   |
| [p2pPort](#p2pport)                                   | number  | p2p protocol tcp port                                     | 24567   |
| [p2pHost](#p2phost)                                   | string  | p2p host                                                  | 0.0.0.0 |
| [rpc](#rpc)                                           | boolean | Enables JSON-RPC server                                   | false   |
| [rpcPort](#rpcport)                                   | number  | JSON-RPC server listening port                            | 3030    |
| [rpcHost](#rpchost)                                   | string  | JSON-RPC server listening host                            | 0.0.0.0 |
| [prometheusPort](#prometheusport)                     | number  | Prometheus exporter port                                  | 9615    |
| [prometheusHost](#prometheushost)                     | string  | Prometheus exporter host                                  | 0.0.0.0 |
| [telemetryURL](#telemetryurl)                         | string  | Telemetry service URL                                     |         |
| [bootnodes](#bootnodes)                               | array   | Boot nodes to bootstrap network from                      |         |
| [resources](#resources)                               | object  | node compute and storage resources to alloacte            |         |

### network

### nodePrivateKeySecretName

### validatorSecretName

### minPeers

### archive

### p2pPort

### p2pHost

### prometheusPort

### prometheusHost

### telemetryURL

### bootnodes

### resources


`resources` allocates compute and storage resources to the node.

| Syntax       | Type   | Description                                 | Default                                                                                    |
| ------------ | ------ | ------------------------------------------- | ------------------------------------------------------------------------------------------ |
| cpu          | string | number of cpu cores this node requires      | `4`                                                                                        |
| cpuLimit     | string | number of cpu cores this node is limited to | `8`                                                                                        |
| memory       | string | memory this node requires                   | `4Gi`                                                                                      |
| memoryLimit  | string | memory this node is limited to              | `8Gi`                                                                                      |
| storage      | string | disk space this node requires               | `250Gi` for non archival node, `4Ti` for archive node                                      |
| storageClass | string | Node volume storage class                   | Cluster's default storage class will be used as defined by cluster admin or cloud provider |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1`), `1500m`, `2`, and `4.`

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than or equal to `memory`.

`storageClass` field is immutable, it cannot be changed after creation.
