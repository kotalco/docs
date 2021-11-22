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

Chainlink node requires a **strong** password for securing access to keystore (wallet) and API. We will store this password in Kubernetes secret `my-password`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-password
stringData:
  password: fE2xXKDnR3ns489X
```

### TLS Certificate

Chainlink optionally requires TLS certificate to secure access to the node API. Using [cert-manager](https://cert-manager.io) we can issue a simple self-signed certificate and store it in a Kubernetes secret:

```yaml
# create self-signed certificate issuer
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: self-signed-issuer
spec:
  selfSigned: {}
---
# create certificate for chainlink node
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: chainlink-node-cert
spec:
  dnsNames:
    - "chainlink-node.svc"
    - "chainlink-node.svc.cluster.local"
  # store tls.crt and tls.key in this kubernetes secret
  secretName: chainlink-node-cert
  issuerRef:
    name: self-signed-issuer
```

## Deploying Chainlink Node

With access to Ethereum Rinkeby node, PostgreSQL database, tls certificate and a strong password. We're ready to deploy our Chainlink node to create jobs and satisfy requests:

```yaml
apiVersion: chainlink.kotal.io/v1alpha1
kind: Node
metadata:
  name: chainlink-node
spec:
  ethereumChainId: 4
  ethereumWsEndpoint: "ws://besu-rinkeby-node:8546"
  linkContractAddress: "0x01BE23585060835E02B77ef475b0Cc51aA1e0709"
  databaseURL: "postgresql://postgres:fE2xXKDnR3ns489X@postgres:5432/postgres?sslmode=disable"
  keystorePasswordSecretName: "my-password"
  certSecretName: "chainlink-node-cert"
  apiCredentials:
    email: "mostafa@kotal.co"
    passwordSecretName: "my-password"
```

