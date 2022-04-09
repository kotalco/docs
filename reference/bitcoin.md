## Node

| Syntax                                  | Type    | Description                                    | Default                                 |
| --------------------------------------- | ------- | ---------------------------------------------- | --------------------------------------- |
| [network](#network) <sup>required</sup> | string  | Bitcoin network to join and sync               |                                         |
| [p2pPort](#p2pport)                     | number  | p2p communications port                        | `8333` for mainnet, `18333` for testnet |
| [p2pHost](#p2phost)                     | string  | p2p communication host                         | 0.0.0.0                                 |
| [rpc](#rpc)                             | boolean | Enables JSON-RPC server                        | false                                   |
| [rpcPort](#rpcport)                     | number  | JSON-RPC server port                           | `8332` for mainnet, `18332` for testnet |
| [rpcHost](#rpchost)                     | string  | JSON-RPC server host                           | 0.0.0.0                                 |
| [rpcUsers](#rpcusers)                   | array   | JSON-RPC users credentials                     |                                         |
| [wallet](#wallet)                       | boolean | Load local wallet and enables wallet RPC calls | false                                   |
| [txIndex](#txindex)                     | boolean | Maintains a full transaction index             | false                                   |
| [resources](#resources)                 | object  | node compute and storage resources to alloacte |                                         |


### network

`network` is Bitcoin network to join and sync. Possible values are `mainnet` and `testnet`.

`network` is immutable, can't be changed after node is created.

### p2pPort

`p2pPort` is p2p communications port.

### p2pHost

`p2pHost` is p2p communications host.

### rpc

`rpc` enables JSON-RPC server.

### rpcPort

`rpcPort` is JSON-RPC server port.

### rpcHost

`rpcHost` is JSON-RPC server host.

### rpcUsers

`rpcUsers` is a list of JSON-RPC users credentials:

| Syntax                                    | Type   | Description                                           |
| ----------------------------------------- | ------ | ----------------------------------------------------- |
| [username](#username)                     | string | JSON-RPC user name                                    |
| [passwordSecretName](#passwordsecretname) | string | Kubernetes secret name holding JSON-RPC user password |


### wallet

`wallet` loads local wallet and enables wallet RPC calls.

### txIndex

`txIndex` maintains a full transaction index.

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
