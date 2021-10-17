{% hint style="info" %}
Kotal uses only `lotus` client for Filecoin nodes, that's why there's no `.spec.client` option.
{% endhint %}

| Syntax                  | Type   | Description                                    | Default |
| ----------------------- | ------ | ---------------------------------------------- | ------- |
| [network](#network)     | string | network to join and sync                       |         |
| [resources](#resources) | object | node compute and storage resources to alloacte |         |

## network

`network` is the network this node should join and sync its blockchain data.

`network` possible values are `mainnet`, `nerpa`, or `calibration`.

## resources

`resources` allocates compute and storage resources to the node.

| Syntax       | Type   | Description                                 | Default                                                                                    |
| ------------ | ------ | ------------------------------------------- | ------------------------------------------------------------------------------------------ |
| cpu          | string | number of cpu cores this node requires      | `4` in nerpa, `8` in other networks                                                        |
| cpuLimit     | string | number of cpu cores this node is limited to | `8` in nerpa, `16` in other networks                                                       |
| memory       | string | memory this node requires                   | `8Gi` in nerpa, `16Gi` in other networks                                                   |
| memoryLimit  | string | memory this node is limited to              | `16Gi` in nerpa, `32Gi` in other networks                                                  |
| storage      | string | disk space this node requires               | `100Gi` in nerpa, `200Gi` in other networks                                                |
| storageClass | string | Node volume storage class                   | Cluster's default storage class will be used as defined by cluster admin or cloud provider |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1`), `1500m`, `2`, and `4.`

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than `memory`.

`storageClass` field is immutable, it cannot be changed after creation.
