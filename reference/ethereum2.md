## BeaconNode

| Syntax                           | Type   | Description                        | Default |
| -------------------------------- | ------ | ---------------------------------- | ------- |
| [network](#network)              | string | Network to join                    |         |
| [client](#client)                | string | Ethereum 2.0 client to use         |         |
| [eth1Endpoints](#eth1endpoints) | array  | Ethereum 1 JSON RPC endpoints      |         |
| [rest](#rest)                    | bool   | Enable REST API server             | false   |
| [restHost](#resthost)           | string | REST API server host               | 0.0.0.0 |
| [restPort](#restport)           | number | REST API server port               | 5051    |
| [rpc](#rpc)                      | bool   | Enables JSON RPC server            | false   |
| [rpcHost](#rpchost)             | string | JSON RPC server host               | 0.0.0.0 |
| [rpcPort](#rpcport)             | number | JSON RPC server port               | 4000    |
| [grpc](#grpc)                    | bool   | GRPC gateway server                | false   |
| [grpcHost](#grpchost)           | string | GRPC gateway server host           | 0.0.0.0 |
| [grpcPort](#grpcport)           | number | GRPC gateway server port           | 3500    |
| [p2pPort](#p2pport)             | number | p2p and discovery port             | 9000    |
| [resources](#resources)          | object | Node compute and storage resources |         |

### network

`network` is Ethereum 2.0 network to join and sync its beacon chain.

### client

`client` is the Ethereum 2.0 client to use.

`client` possible values are `teku`,`prysm`,`lighthouse`, and `nimbus`.

### eth1Endpoints

`eth1Endpoints` is array of Ethereum 1 JSON RPC endpoints.

{% hint style="info" %}
`prysm`, `teku`, and `lighthouse` clients support multiple endpoints in `eth1Endpoints`.

`nimbus` client supports only 1 endpoint in `eth1Endpoints`.
{% endhint %}

### rest

`rest` enables REST API server.

{% hint style="info" %}
REST API server is only supported by `teku` and `lighthouse`.
{% endhint %}


### restHost

`restHost` is the REST API server host.

### restPort

`restPort` is the REST API server port.

### rpc

`rpc` enables JSON RPC server.

{% hint style="info" %}
JSON RPC server is only supported by `nimbus` and `prysm`.
{% endhint %}

### rpcHost

`rpcHost` is the JSON RPC server host.

### rpcPort

`rpcPort` is the JSON RPC server port.

### grpc

`grpc` enables GRPC gateway server.

{% hint style="info" %}
GRPC gateway is only supported by `prysm` client.
{% endhint %}

### grpcHost

`grpcHost` is the GRPC gateway server host.

### grpcPort

`grpcPort` is the GRPC gateway server port.

### p2pPort

`p2pPort` is the p2p and discovery port.

### resources

`resources` allocates compute and storage resources to the node.

`resources` object has the following fields:

| Syntax      | Type   | Description                                 | Defalt  |
| ----------- | ------ | ------------------------------------------- | ------- |
| cpu         | string | number of cpu cores this node requires      | `4`     |
| cpuLimit    | string | number of cpu cores this node is limited to | `8`     |
| memory      | string | memory this node requires                   | `8Gi`   |
| memoryLimit | string | memory this node is limited to              | `16Gi`  |
| storage     | string | disk space this node requires               | `200Gi` |
| storageClass | string | Node volume storage class                  | Cluster's default storage class will be used as defined by cluster admin or cloud provider  |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1` core), `1500m` which is 1.5 core, `2` cores, and `4` cores.

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than `memory`.

`storageClass` field is immutable, it cannot be changed after creation.

## Validator

| Syntax                                                | Type   | Description                             | Default          |
| ----------------------------------------------------- | ------ | --------------------------------------- | ---------------- |
| [network](#network)                                   | string | Network to validate blocks for          |                  |
| [client](#client)                                     | string | Ethereum 2.0 client to use              |                  |
| [beaconEndpoints](#beaconendpoints)                   | array  | List of beacon node endpoints           |                  |
| [graffiti](#graffiti)                                 | string | Text to include in proposed blocks      | Powered by Kotal |
| [walletPasswordSecretName](#walletpasswordsecretname) | string | Wallet password kubernetes secret       |                  |
| [keystores](#keystores)                               | array  | Validator keystores                     |                  |
| [resources](#resources)                               | object | Validator compute and storage resources |                  |

### network

`network` is the Network to validate blocks for.

### client

`client` is the Ethereum 2.0 client to use.

### beaconEndpoints

`beaconEndpoints` is a list of beacon node endpoints.

{% hint style="info" %}
`lighthouse` is the only client that supports multiple endpoints. All other clients supports only a single endpoint.
{% endhint %}

### graffiti

`graffiti` is the text to include in proposed blocks.

### walletPasswordSecretName

`walletPasswordSecretName` is `prysm` wallet password kubernetes secret.

{% hint style="info" %}
`walletPasswordSecretName` is supported only by `prysm` client.
{% endhint %}

{% hint style="warning" %}
Wallet password secret must be deployed before deploying the validator.

Wallet password secret must contain the password in key named `password`.

```bash
# create k8s secret from password value
kubectl create secret wallet-password --from-literal=password=<actual-password>
# create k8s secret from password file
kubectl create secret wallet-password --from-file=password=<password-file-path>
```
{% endhint %}


### keystores

`keystores` is array of keystore objects.

| Syntax     | Type   | Description                                              |
| ---------- | ------ | -------------------------------------------------------- |
| secretName | string | kubernetes secret name holding `keystore` and `password` |
| publicKey  | string | Validator public key in hexadecimal                      |

{% hint style="info" %}
Validator keystore public key is required in case of `client: lighthouse`
{% endhint %}


{% hint style="warning" %}
Keystore secret must be deployed before deploying the validator.

Keystore secret must contain the BLS12-381 keystore JSON file in data field key called `keystore` key, and password in `password` key.

```bash
# create k8s secret from keystore and password file
kubectl create secret my-validator --from-file=keystore=/path/to/keystore.json --from-file=password=/path/to/password.txt
```

{% endhint %}

### resources

`resources` allocates compute and storage resources to the node.

`resources` object has the following fields:

| Syntax      | Type   | Description                                 | Defalt  |
| ----------- | ------ | ------------------------------------------- | ------- |
| cpu         | string | number of cpu cores this node requires      | `4`     |
| cpuLimit    | string | number of cpu cores this node is limited to | `8`     |
| memory      | string | memory this node requires                   | `8Gi`   |
| memoryLimit | string | memory this node is limited to              | `16Gi`  |
| storage     | string | disk space this node requires               | `200Gi` |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1` core), `1500m` which is 1.5 core, `2` cores, and `4` cores.

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than `memory`.
