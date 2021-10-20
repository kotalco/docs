## Node

{% hint style="info" %}
Kotal uses only `lotus` client for Filecoin nodes, that's why there's no `.spec.client` option.
{% endhint %}

| Syntax                                    | Type    | Description                                    | Default |
| ----------------------------------------- | ------- | ---------------------------------------------- | ------- |
| [network](#network)                       | string  | network to join and sync                       |         |
| [api](#api)                               | boolean | enables API server                             | false   |
| [apiPort](#apiport)                       | number  | API server listening port                      | 1234    |
| [apiHost](#apihost)                       | string  | API server host                                | 0.0.0.0 |
| [apiRequestTimeout](#apirequesttimeout)   | number  | API request timeout in seconds                 | 30      |
| [disableMetadataLog](#disablemetadatalog) | boolean | disables metadata logging                      | false   |
| [p2pPort](#p2pport)                       | string  | p2p port                                       | 4444    |
| [p2pHost](#p2phost)                       | string  | p2p host                                       | 0.0.0.0 |
| [ipfsPeerEndpoint](#ipfspeerendpoint)     | string  | ipfs peer endpoint                             |         |
| [ipfsOnlineMode](#ipfsonlinemode)         | boolean | ipfs online mode                               | false   |
| [ipfsForRetrieval](#ipfsforretrieval)     | boolean | use ipfs for retrieval                         | false   |
| [resources](#resources)                   | object  | node compute and storage resources to alloacte |         |

### network

`network` is the network this node should join and sync its blockchain data.

`network` possible values are `mainnet`, and `calibration`.

{% hint style="warning" %}
`butterfly` and `nerpa` networks have been deprecated 🥺
{% endhint %}

### api

`api` enables API server.

### apiPort

`apiPort` is API server listening port.

### apiHost

`apiHost` is API server host.

### apiRequestTimeout

`apiRequestTimeout` is API request timeout in seconds.

### disableMetadataLog

`disableMetadataLog` disables metadata logging.

### p2pPort

`p2pPort` is p2p port.

### p2pHost

`p2pHost` is p2p host.

### ipfsPeerEndpoint

`ipfsPeerEndpoint` ipfs peer endpoint.


### ipfsOnlineMode

`ipfsOnlineMode` sets sets ipfs online mode.

### ipfsForRetrieval

`ipfsForRetrieval` uses ipfs for retrieval.

### resources

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

`memoryLimit` can't be less than or equal to `memory`.

`storageClass` field is immutable, it cannot be changed after creation.
