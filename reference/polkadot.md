## Node

| Syntax                                                | Type   | Description                                             | Default |
| ----------------------------------------------------- | ------ | ------------------------------------------------------- | ------- |
| [network](#network) <sup>required</sup>               | string | polkadot network/chain to join and sync                 |         |
| [p2pPort](#p2pPort)                                   | string | p2p protocol tcp port                                   | 30333   |
| [nodePrivateKeySecretName](#nodePrivateKeySecretName) | string | Kubernetes secret name holding node Ed25519 private key |         |
| [resources](#resources)                               | object | node compute and storage resources to alloacte          |         |

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

`polkadot-node-key` secret can be used in your polkadot `Node` by updating `.spec` with `nodePrivateKeySecretName: polkadot-node-key`


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

`memoryLimit` can't be less than `memory`.

`storageClass` field is immutable, it cannot be changed after creation.
