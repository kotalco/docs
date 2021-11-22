## Prerequisites

### Ethereum Node

Chainlink node requires access to an EVM chain node with WebSocket server enabled. Here's how to deploy a simple Ethereum Rinkeby node with WebSocket server enabled:

```yaml
apiVersion: ethereum.kotal.io/v1alpha1
kind: Node
metadata:
  name: besu-rinkeby-node
spec:
  network: rinkeby
  client: besu
  ws: true
```

After deploying this node, it can be accessed in-cluster using `ws://besu-rinkeby-node:8546` 🔥. Read [Ethereum guide](../ethereum/README.md) for more details on how to deploy public and private network nodes.

### PostgreSQL Database

Chainlink requires access to [PostgreSQL](https://www.postgresql.org) database instance.

You can use a hosted database a service to obtain PostgreSQL database:

* [Amazon AWS](https://aws.amazon.com/getting-started/hands-on/create-connect-postgresql-db/)
* [Azure](https://docs.microsoft.com/en-us/azure/postgresql/quickstart-create-server-database-portal)
* [Google Cloud](https://cloud.google.com/community/tutorials/setting-up-postgres)

You can also deploy your own PostgreSQL database instance from official PostgreSQL [docker image](https://hub.docker.com/_/postgres).

### Keystore and API Password

Chainlink node requires a strong **password** for securing access to keystore (wallet) and API. We will store this password in Kubernetes secret `my-password`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-password
stringData:
  password: fE2xXKDnR3ns489X
```

