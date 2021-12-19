## Node

| Syntax                                                    | Type    | Description                                        | Default |
| --------------------------------------------------------- | ------- | -------------------------------------------------- | ------- |
| [ethereumChainId](#ethereumchainid)                       | number  | Ethereum chain id                                  |         |
| [linkContractAddress](#linkcontractaddress)               | string  | Link contract address                              |         |
| [ethereumWsEndpoint](#ethereumwsendpoint)                 | string  | Ethereum node WebSocket server endpoint            |         |
| [ethereumHttpEndpoints](#ethereumhttpendpoints)           | array   | Ethereum HTTP endpoints                            |         |
| [databaseURL](#databaseurl)                               | string  | Postgres database connection URL                   |         |
| [keystorePasswordSecretName](#keystorepasswordsecretname) | string  | k8s secret name that holds keystore password       |         |
| [apiCredentials](#apicredentials)                         | object  | API credentials                                    |         |
| [corsDomains](#corsdomains)                               | array   | Domains from which to accept cross origin requests | *       |
| [certSecretName](#certsecretname)                         | string  | k8s secret name that holds tls.key and tls.crt     |         |
| [apiPort](#apiport)                                       | number  | Port used for node API and GUI                     | 6688    |
| [tlsPort](#tlsport)                                       | number  | Port used for HTTPS connections                    | 6689    |
| [p2pPort](#p2pport)                                       | number  | Port used for p2p communcations                    | 30303   |
| [secureCookies](#securecookies)                           | boolean | Enable secure cookies for authentication           | false   |
| [logging](#logging)                                       | string  | Logging verboisty level                            | info    |
| [resources](#resources)                                   | object  | node compute and storage resources to alloacte     |         |

### ethereumChainId

`ethereumChainId` is ethereum chain id.

### linkContractAddress

`linkContractAddress` is Link contract address.

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
