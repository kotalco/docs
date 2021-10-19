## Peer

{% hint style="info" %}
Kotal uses only `go-ipfs` client for IPFS peers, that's why there's no `.spec.client` option.
{% endhint %}

| Syntax                                    | Type   | Description                                                       | Default           |
| ----------------------------------------- | ------ | ----------------------------------------------------------------- | ----------------- |
| [initProfiles](#initprofiles)             | Array  | List of initial configuration profiles                            | default-datastore |
| [profiles](#profiles)                     | Array  | List of configuration profiles to apply after peer initialization |                   |
| [apiHost](#apihost)                       | string | API server host                                                   | 0.0.0.0           |
| [apiPort](#apiport)                       | number | API server port                                                   | 5001              |
| [gatewayHost](#gatewayhost)               | string | Local ipfs gateway host                                           | 0.0.0.0           |
| [gatewayPort](#gatewayport)               | number | Local ipfs gateway port                                           | 8080              |
| [routing](#routing)                       | string | Content routing mechanism                                         | dht               |
| [swarmKeySecretName](#swarmkeysecretname) | string | Name of the k8s secret holding swarm secret key                   |                   |
| [resources](#resources)                   | object | Compute and storage resources                                     |                   |

### initProfiles

`initProfiles` is al list of initial ipfs configuration profile.

`initialProfiles` available values are `server`, `randomports`, `default-datastore`, `local-discovery`, `test`, `default-networking`, `flatfs`, `badgerds`, and `lowpower`.

`initProfiles` can't be updated (immutable).

### profiles

`profiles` is the list of configuration profiles to apply after peer initialization.

`profiles` available values are `server`, `randomports`, `default-datastore`, `local-discovery`, `test`, `default-networking`, `flatfs`, `badgerds`, and `lowpower`.

### apiHost

`apiHost` is API server host.

{% hint style="info" %}
If you set `apiHost` to host other than `0.0.0.0`, api calls forwarded to the container won't hit the API server. This is useful if you want to disallow calls to API server.
{% endhint %}

### apiPort

`apiPort` is API server port.

### gatewayHost

`gatewayHost` is local ipfs gateway host.

{% hint style="info" %}
If you set `gatewayHost` to host other than `0.0.0.0`, gateway won't work. This is useful if you want to disable to access gateway from outside.
{% endhint %}

### gatewayPort

`gatewayPort` is API server port.

### routing

`routing` is the content routing mechanism.

`routing` available values are `none`, `dht`, `dhtclient`, `dhtserver.`

### swarmKeySecretName

`swarmKeySecretName` is the kubernetes secret name that's holding the swarm key in a key called `secret`.

```bash
kubectl create secret generic swarm-key --from-literal=secret=$w@rmk3y
```

### resources

`resources` allocates compute and storage resources to the peer.

`resources` object has the following fields:

| Syntax       | Type   | Description                                 | Defalt                                                                                     |
| ------------ | ------ | ------------------------------------------- | ------------------------------------------------------------------------------------------ |
| cpu          | string | number of cpu cores this peer requires      | `1`                                                                                        |
| cpuLimit     | string | number of cpu cores this peer is limited to | `2`                                                                                        |
| memory       | string | memory this peer requires                   | `2Gi`                                                                                      |
| memoryLimit  | string | memory this peer is limited to              | `4Gi`                                                                                      |
| storage      | string | disk space this peer requires               | `10Gi`                                                                                     |
| storageClass | string | Node volume storage class                   | Cluster's default storage class will be used as defined by cluster admin or cloud provider |

Memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1` core), `1500m` which is 1.5 core, `2` cores, and `4` cores.

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than or equal to `memory`.

`storageClass` field is immutable, it cannot be changed after creation.

## ClusterPeer

{% hint style="info" %}
Kotal uses only `ipfs-cluster-service` for IPFS cluster peers that runs along with `go-ipfs`, that's why there's no `.spec.client` option.
{% endhint %}

| Syntax                                           | Type   | Description                                             | Default                    |
| ------------------------------------------------ | ------ | ------------------------------------------------------- | -------------------------- |
| [id](#id)                                        | string | Cluster peer ID                                         |                            |
| [privateKeySecretName](#privatekeysecretname)    | string | Name of the k8s secret holding cluster peer privatekey  | `*` if consensus is `crdt` |
| [trustedPeers](#trustedpeers)                    | array  | Peer IDs that can manage the pinset in `crdt` consensus |                            |
| [bootstrapPeers](#bootstrappeers)                | array  | Peers to connect to on startup                          |                            |
| [consensus](#consensus)                          | string | Cluster consensus algorithm                             | `crdt`                     |
| [peerEndpoint](#peerendpoint) required           | string | ipfs peer http API endpoint                             |                            |
| [clusterSecretName](#clustersecretname) required | string | Name of the k8s secret holding cluster secret           |                            |
| [resources](#resources)                          | object | Compute and storage resources                           |                            |

### id

`id` is cluster peer id derived from private key.

`id` is required if `privateKeySecretName` is provided.

`id` can be generated using [ipfs-key](https://github.com/whyrusleeping/ipfs-key) tool.

```bash
ipfs-key -type ed25519 | base64
```

It will return an output similar to the following:

```text
Generating a 2048 bit ed25519 key...
Success!
ID for generated key: 12D3KooWT2bqgwZPxHthAGBV9Ut8ZLraz1LARtB7vG3mF26Mtof1
CAESQLepaunFGa/PI0oNS3plrbVSInqab/X/U1laEDe2V2cL/7kbF9H6x3xBiwpbVnYT/jDA8EhAznXALlbwzEsuKaw=
```

### privateKeySecretName

`privateKeySecretName` is the name of the k8s secret holding **base64** cluster peer privatekey in a key called `key`.

`privateKeySecretName` is required if `id` is provided.

`privateKeySecretName` can be generated using [ipfs-key](https://github.com/whyrusleeping/ipfs-key) tool.

```bash
ipfs-key -type ed25519 | base64
```

It will return an output similar to the following:


```
Generating a 2048 bit ed25519 key...
Success!
ID for generated key: 12D3KooWT2bqgwZPxHthAGBV9Ut8ZLraz1LARtB7vG3mF26Mtof1
CAESQLepaunFGa/PI0oNS3plrbVSInqab/X/U1laEDe2V2cL/7kbF9H6x3xBiwpbVnYT/jDA8EhAznXALlbwzEsuKaw=
```

### trustedPeers

`trustedPeers` is a list of peer IDs that can manage the cluster pinset in `crdt` consensus clusters.

`trustedPeers` is ignored in `raft` consensus clusters.

`trustedPeers` default value is `*` which is trust all peers.

### bootstrapPeers

`bootstrapPeers` is a list of peers to connect to on startup.

{% hint style="warning" %}
`bootstrapPeers` will be trusted peers in `crdt` clusters.
{% endhint %}

### consensus

`consensus` is the cluster consensus algorithm.

### peerEndpoint

`peerEndpoint` id ipfs peer http API endpoint.

`peerEndpoint` is required for the cluster peer to function correctly.

### clusterSecretName

`clusterSecretName` is the k8s secret name holding **32-bit hex-encoded** _(without 0x)_ cluster secret in a key called `secret`.

`clusterSecretName` can be generated using openssl tool:

```bash
CLUSTER_SECRET=$(openssl rand -hex 32)
kubectl create secret generic cluster-secret --from-literal=secret=$CLUSTER_SECRET
```

### resources

`resources` allocates compute and storage resources to the peer.

`resources` object has the following fields:

| Syntax      | Type   | Description                                 | Defalt |
| ----------- | ------ | ------------------------------------------- | ------ |
| cpu         | string | number of cpu cores this peer requires      | `1`    |
| cpuLimit    | string | number of cpu cores this peer is limited to | `2`    |
| memory      | string | memory this peer requires                   | `2Gi`  |
| memoryLimit | string | memory this peer is limited to              | `4Gi`  |
| storage     | string | disk space this peer requires               | `10Gi` |

Memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1` core), `1500m` which is 1.5 core, `2` cores, and `4` cores.

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than or equal to `memory`.
