## Node

| Syntax                                                | Type    | Description                                                           | Default                             |
| ----------------------------------------------------- | ------- | --------------------------------------------------------------------- | ----------------------------------- |
| [network](#network)                                   | string  | Public network name to join, like `mainnet`, `rinkeby`, and `goerli`  |                                     |
| [highlyAvailable](#highly-available)                  | boolean | Ethereum nodes will be scheduled on different kubernetes nodes        | `false`                             |
| [topologyKey](#topology-key)                          | string  | kubernetes node label key used to distribute ethereum nodes           | `topology.kubernetes.io/zone`       |
| [genesis](#genesis)                                   | object  | Genesis block configuration                                           |                                     |
| [bootnodes](#bootnodes)                               | array   | ethereum node URLs for p2p discovery bootstrap                        |                                     |
| [client](#client)                                     | string  | ethereum client powering the node                                     |                                     |
| [coinbase](#coinbase)                                 | string  | ethereum account to which mining rewards are paid                     |                                     |
| [corsDomains](#corsdomains)                           | array   | domains from which to accept cross origin requests (browser enforced) | `*`                                 |
| [graphql](#graphql)                                   | boolean | enable GraphQL server                                                 | `false`                             |
| [graphqlPort](#graphqlport)                           | number  | GraphQL server listening port                                         | `8547`                              |
| [hosts](#hosts)                                       | array   | hostnames to whitelist for RPC access (server enforced)               | `*`                                 |
| [import](#import)                                     | object  | ethereum account to import for `geth` node                            |                                     |
| [logging](#logging)                                   | string  | node logging verbosity level                                          | `info`                              |
| [miner](#miner)                                       | boolean | node is mining or signing blocks ?                                    | false                               |
| [nodePrivateKeySecretName](#nodePrivateKeySecretName) | string  | name of kubernetes secret holding node private key                    |                                     |
| [p2pPort](#p2pport)                                   | string  | node p2p port                                                         | `30303`                             |
| [resources](#resources)                               | object  | node compute and storage resources to alloacte                        |                                     |
| [rpc](#rpc)                                           | boolean | enable HTTP RPC server                                                | `false`                             |
| [rpcPort](#rpcport)                                   | number  | HTTP RPC server listening port                                        | `8545`                              |
| [rpcAPI](#rpcapi)                                     | array   | services to enable                                                    | `web3`, `eth`, and `net`            |
| [staticNodes](#staticnodes)                           | array   | set of trusted ethereum nodes to maintain connection to               |                                     |
| [syncMode](#syncmode)                                 | string  | blockchain synchronization mode                                       | `fast` in public, `full` in private |
| [ws](#ws)                                             | boolean | enable web socket server                                              | `false`                             |
| [wsPort](#wsport)                                     | number  | web socket server listening port                                      | `8546`                              |
| [wsAPI](#wsapi)                                       | array   | services to enable                                                    | `web3`, `eth`, and `net`            |


### network

`network` is the public network name to join, like `rinkeby`.

`network` can't be provided in private networks, where `.spec.genesis` is not null.

`network` can't be updated (immutable).

### highly available

`highlyAvailable` controls if Ethereum nodes will be scheduled on different Kubernetes nodes.

### topology key

`topologyKey` is Kubernetes node label key used to distribute ethereum nodes pods on different kubernetes nodes.


### bootnodes

`bootnodes` is ethereum node URLs for p2p network discovery and bootstraping.

`bootnodes` accepts ethereum node URL `enodeURL` or a reference to kotal `Node` in the form of `name.namespace` where namespace is optional if referenced node is in the same namespace.

{% hint style="warning" %}
Ethereum clients have hardcoded bootnodes for public main and test networks. `bootnodes` will override these bootnodes.
{% endhint %}


### client

{% hint style="warning" %}
Support for OpenEthereum (parity) client has been deprecated. OpenEthereum team is working with Erigon on a smooth transition path for users. More information can be found [here](https://medium.com/openethereum/gnosis-joins-erigon-formerly-turbo-geth-to-release-next-gen-ethereum-client-c6708dd06dd)
{% endhint %}

`client` is the Ethereum client name powering the node.

`client` possible values are `besu`, `geth`, `nethermind`.

Only `besu` client can be used if network consensus is `ibft2`.

Only `besu` can be used in fixed difficulty proof of work networks, where `spec.genesis.ethash.fixedDifficulty` is not null.

### coinbase

`coinbase` is ethereum account to which mining rewards are paid.

`coinbase` is required if the node is mining `miner: true`.

### corsDomains

`corsDomains` is a list of domains from which to accept cross-origin requests (browser enforced).

Default value `*` will be used if HTTP RPC server is enabled `rpc: true` or web socket server is enabled `ws: true` or graphQL server is enabled `graphql: true`.

### graphql

`graphql` enables the GraphQL server.

{% hint style="warning" %}

* Nethermind client doesn't support GraphQL.
* Geth (Go-Ethereum) GraphQL server can be used only if RPC is enabled as of geth [v1.9.19 release notes](https://github.com/ethereum/go-ethereum/releases/tag/v1.9.19)

{% endhint %}


### graphqlPort

`graphqlPort` GraphQL server listening port.

The default value `8547` will be used if the graphQL server is enabled `graphql: true`.

### hosts

`hosts` is a list of host names to whitelist for RPC access (server enforced).

### import

`import` is the ethereum account to import. Only for nodes running with `geth` or `nethermind`.

During account creation, it will be encrypted with the password, and during import it will be unlocked using the same password.

Nodes that import accounts can't enable HTTP RPC server, web socket server, or GraphQL server to prevent funds drainage if exposed to the internet.

Account must be imported if the node is running with `geth` or `nethermind` clients wants to be a signer or miner.

| Syntax               | Type   | Description                                                                                               |
| -------------------- | ------ | --------------------------------------------------------------------------------------------------------- |
| privateKeySecretName | string | name of the k8s secret holding account private key in hexadecimal (without 0x) in data field called `key` |
| passwordSecretName   | string | name of the k8s secret holding encryption secret in data field called `password`                          |

### logging

`logging` is Ethereum node logging verbosity level.

`logging` possible values are `off`, `fatal`, `error`, `warn`, `debug`, `info`, `trace` and `all`.

Different clients support different logging vrbosity levels as shown in the following table:

| Logging/Client | Hyperledger Besu | Go Ethereum | Nethermind |
| -------------- | ---------------- | ----------- | ---------- |
| `off`          | ✔️                | ✔️           | ❌          |
| `fatal`        | ✔️                | ❌           | ❌          |
| `error`        | ✔️                | ✔️           | ✔️          |
| `warn`         | ✔️                | ✔️           | ✔️          |
| `debug`        | ✔️                | ✔️           | ✔️          |
| `info`         | ✔️                | ✔️           | ✔️          |
| `trace`        | ✔️                | ❌           | ✔️          |
| `all`          | ✔️                | ✔️           | ❌          |

### miner

`miner` enables node mining or signing blocks.

### nodePrivateKeySecretName

`nodePrivateKeySecretName` is the node private key.

`nodePrivateKeySecretName` is required if the node is a boot node `bootnode: true`, or if besu node with `client: besu` is a signer in proof of authority clique network or validator in ibft2 network.

### p2pPort

`p2pPort` is node p2p port for communication (TCP) and discovery (UDP).

### resources

`resources` allocates compute and storage resources to the node.

| Syntax       | Type   | Description                                 | Default                                                                                                               |
| ------------ | ------ | ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| cpu          | string | number of cpu cores this node requires      | `2` in private, `4` in public                                                                                         |
| cpuLimit     | string | number of cpu cores this node is limited to | `3` in private, `6` in public                                                                                         |
| memory       | string | memory this node requires                   | `4Gi` in private, `8Gi` in public                                                                                     |
| memoryLimit  | string | memory this node is limited to              | `8Gi` in private, `16Gi` in public                                                                                    |
| storage      | string | disk space this node requires               | `100Gi` in private, `6Ti` in mainnet with full sync, `750Gi` in mainnet wit fast sync, `25Gi` in public test networks |
| storageClass | string | Node volume storage class                   | Cluster's default storage class will be used as defined by cluster admin or cloud provider                            |

memory and storage requests and limits must use the pattern `^[1-9][0-9]*[KMGTPE]i$` for example `1500Mi`, `30Gi`, and `1Ti`.

cpu requests and limits must use the pattern `^[1-9][0-9]*m?$` for example `1000m` (which is equal to `1`), `1500m`, `2`, and `4.`

`cpuLimit` can't be less than `cpu`.

`memoryLimit` can't be less than or equal to `memory`.

`storageClass` field is immutable, it cannot be changed after creation.

### rpc

`rpc` enables the HTTP RPC server.

### rpcPort

`rpcPort` is the HTTP RPC server listening port.

Default value `8545` will be used if the HTTP RPC server is enabled with `rpc: true`.

### rpcAPI

`rpcAPI` is a list of RPC services to enable.

Default value `["web3", "eth", "net]` will be used if HTTP RPC server is enabeld with `rpc: true`.

### staticNodes

`staticNodes` is a set of trusted ethereum nodes to maintain connection to.

{% hint style="info" %}
Static nodes are exempt from maximum peer and remote connection limits. Ethereum clients periodically initiating a connection to any unconnected static node.
{% endhint %}

`staticNodes` accepts ethereum node URL `enodeURL` or a reference to kotal `Node` in the form of `name.namespace` where namespace is optional if referenced node is in the same namespace.

### syncMode

{% hint style="warning" %}
Nethermind client [removed support for beam sync](https://github.com/NethermindEth/nethermind/commit/0f8c927bb039c99c554b5dea5eaaac1e7d13abf3) which was activated by `syncMode: light`.
{% endhint %}

`syncMode` is Blockchain synchronization mode.

`syncMode` possible values are `light`, `full`, `snap` or `fast`.

`light` and `snap` sync modes are only supported by go-ethereum client.

### ws

`ws` enables the web socket server.

### wsPort

`wsPort` is the web socket server listening port.

The default value `8546` is used if the web socket server is enabled with `ws: true`.

### wsAPI

`wsAPI` is a list of rpc services to enable.

the default value `["web3", "eth", "net]` will be used if the web socket server is enabeld with `ws: true`.


## genesis

`genesis` is the genesis block configuration.

Genesis block configuration `genesis` is required in private networks.

| Syntax                    | Type   | Description                                                                                                                        | Default      |
| ------------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| [chainId](#chainid)       | number | used in transaction signature to prevent transactions reply [eip155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md) |              |
| [networkId](#networkid)   | number | used in network p2p communications                                                                                                 |              |
| [coinbase](#coinbase)     | string | benefeciary (ethereum address) of mining reward                                                                                    | `address(0)` |
| [difficulty](#difficulty) | string | difficulty (hexadecimal number) of the genesis block                                                                               | `0x1`        |
| [mixHash](#mixhash)       | string | hash (hexadecimal) combined with nonce to prove effort spent to create block                                                       | `0x00..00`   |
| [gasLimit](#gaslimit)     | string | total gas limit (hexadecimal number) for all transactions in a block                                                               | `0x47b760`   |
| [nonce](#nonce)           | string | random hexadecimal number used in block computation                                                                                | `0x0`        |
| [timestamp](#timestamp)   | string | genesis block creation date (hexadecimal)                                                                                          | `0x0`        |
| [accounts](#accounts)     | Array  | array of accounts to prefund and store code                                                                                        |              |
| [forks](#forks)           | object | supported forks and corresponding block number                                                                                     |              |
| [ethash](#ethash)         | object | Proof of Work consensus configuration                                                                                              |              |
| [clique](#clique)         | object | Proof of Authority consensus configuration                                                                                         |              |
| [ibft2](#ibft2)           | object | IBFT2 consensus configuration                                                                                                      |              |

### chainId

`chainId` is the chain id value used in transaction signature to prevent transactions reply [eip155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md)

`chainId` is required.

`chainId` can't reuse existing public network chain id to avoid transaction replay.

| Network          | Chain id |
| ---------------- | -------- |
| mainnet          | 1        |
| ropsten          | 3        |
| rinkeby          | 4        |
| goerli           | 5        |
| kotti            | 6        |
| ethereum classic | 61       |
| Mordor           | 63       |
| development      | 2018     |

### networkId

`id` is the network id used for p2p communications between network nodes in private networks.

`id` is required in private networks.

`id` can't be provided while joining a public network.

`id` can't be updated (immutable).

### coinbase

`coinbase` is the beneficiary (ethereum address) of mining reward.

`coinbase` is optional.

### difficulty

`difficulty` is the difficulty of the genesis block.

`difficulty` is optional.

### mixHash

`mixHash` is combined with the nonce to prove the effort spent to create a block.

`mixHash` is optional.

### gasLimit

`gasLimit` is the total gas limit for all transactions in a block.

`gasLimit` is optional.

### nonce

`nonce` is a random number used in block computation.

`nonce` is optional.

### timestamp

`timestamp` is block creation date.

`timestamp` is optional.

### forks

`forks` is an object, where the key is fork name and the value is the block number at which to activate this fork.

`forks` is optional.

if `forks` is missing, all forks will be activated at block 0 (genesis block) except DAO.

Later forks like `muirglacier` can't be activated before earlier forks like `homestead`. They must be ordered as in the following table:

| Syntax         | Type   | Description                                                                                                                                              |
| -------------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| homestead      | number | [Homestead](https://blog.ethereum.org/2016/02/29/homestead-release/) fork activation block number                                                        |
| dao            | number | [DAO](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-779.md) fork activation block number                                                         |
| eip150         | number | [eip150](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-150.md) fork activation block number                                                      |
| eip155         | number | [eip155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md) fork activation block number                                                      |
| eip158         | number | [eip158](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-158.md) fork activation block number                                                      |
| byzantium      | number | [Byzantium](https://blog.ethereum.org/2017/10/12/byzantium-hf-announcement/) fork activation block number                                                |
| constantinople | number | [Constantipole](https://blog.ethereum.org/2019/02/22/ethereum-constantinople-st-petersburg-upgrade-announcement/) fork activation block number           |
| petersburg     | number | [Petersburg](https://blog.ethereum.org/2019/02/22/ethereum-constantinople-st-petersburg-upgrade-announcement/) fork activation block number              |
| istanbul       | number | [Istanbul](https://eips.ethereum.org/EIPS/eip-1679) fork activation block number                                                                         |
| muirglacier    | number | [Muir Glacier](https://eips.ethereum.org/EIPS/eip-2387) fork activation block number                                                                     |
| berlin         | number | [Berlin](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md) fork activation block number               |
| london         | number | [London](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london.md) fork activation block number               |
| arrowGlacier   | number | [Arrow Glacier](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/arrow-glacier.md) fork activation block number |

### accounts

`accounts` is an array of accounts to fund or store code.

`accounts` is optional.

`accounts` is recommended for networks where signers or validators are not rewarded with eth.

a single account has no defaults.

| Syntax  | Type   | Description                                                                               |
| ------- | ------ | ----------------------------------------------------------------------------------------- |
| address | string | ethereum address                                                                          |
| balance | string | account balance in hexadecimal                                                            |
| code    | string | bytecode in hexadecimal                                                                   |
| storage | map    | key is the storage location in hexadecimal, and value in hexadecimal is the storage value |

### ethash

`ethash` is Proof of Work consensus engine configuration.

`ethash` is optional.

`ethash` can be set only in proof of work private networks.

`ethash.fixedDifficulty` has no default value.

{% hint style="warning" %}
`ethash.fixedDifficulty` is only supported by Hyperledger Besu Client `client: besu`
{% endhint %}

| Syntax          | Type   | Description                                | Default |
| --------------- | ------ | ------------------------------------------ | ------- |
| fixedDifficulty | number | fixed difficulty used in block computation |         |

### clique

`clique` is Proof of Authority clique consensus engine configuration.

`clique` is optional.

`clique` can be set only in proof of authority clique private networks.

At least one signer in `clique.signers` is required.

| Syntax      | Type   | Description                                     | Default |
| ----------- | ------ | ----------------------------------------------- | ------- |
| blockPeriod | number | block time in seconds                           | 15      |
| epochLength | number | number of blocks after which to reset all votes | 1000    |
| signers     | Array  | array of ethereum addresses                     |

### ibft2

`ibft2` is IBFT2 engine configuration.

`ibft2` is optional.

`ibft2` can be set only in IBFT2 private networks.

At least one validator in `ibft2.validators` is required.

| Syntax                    | Type   | Description                                                            | Default |
| ------------------------- | ------ | ---------------------------------------------------------------------- | ------- |
| blockPeriod               | number | block time in seconds                                                  | 15      |
| epochLength               | number | number of blocks after which to reset all votes                        | 1000    |
| validators                | array  | array of ethereum addresses                                            |
| requestTimeout            | number | timeout for each consensus round in seconds                            | 10      |
| messageQueueLimit         | number | message queue limit                                                    | 1000    |
| duplicateMessageLimit     | number | duplicate messages limit                                               | 100     |
| futureMessagesLimit       | number | future messages buffer limit                                           | 1000    |
| futureMessagesMaxDistance | number | maximum height from current chain height for buffering future messages | 10      |
