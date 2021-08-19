## Peer

| Syntax                                    | Type   | Description                                                       | Default           |
| ----------------------------------------- | ------ | ----------------------------------------------------------------- | ----------------- |
| [initProfiles](#initprofiles)             | Array  | List of initial configuration profiles                            | default-datastore |
| [profiles](#profiles)                     | Array  | List of configuration profiles to apply after peer initialization |                   |
| [apiHost](#apihost)                       | string | API server host                                                   | 0.0.0.0           |
| [apiPort](#apiport)                       | number | API server port                                                   | 5001              |
| [gatewayHost](#gatewayhost)               | string | Local ipfs gateway host                                           | 0.0.0.0           |
| [gatewayPort](#gatewayport)               | number | Local ipfs gateway port                                           | 8080              |
| [routing](#routing)                       | string | Content routing mechanism                                         | dht               |
| [swarmKeySecretName](#swarmkeysecretname) | string | Name of the k8s secret holding swarm key                          |                   |
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

`swarmKeySecretName` is the kubernetes secret name tha holds the swarm key in a key called `key`.

```bash
kubectl create secret generic swarm-key --from-literal=secret=$w@rmk3y
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

## ClusterPeer
