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

{% code title="chainlink.yaml" %}
```yaml
apiVersion: chainlink.kotal.io/v1alpha1
kind: Node
metadata:
  name: chainlink-node
spec:
  ethereumChainId: 4
  ethereumWsEndpoint: "ws://besu-rinkeby-node:8546"
  linkContractAddress: "0x01BE23585060835E02B77ef475b0Cc51aA1e0709"
  databaseURL: "postgresql://username:password@server:port/name"
  keystorePasswordSecretName: "my-password"
  certSecretName: "chainlink-node-cert"
  apiCredentials:
    email: "mostafa@kotal.co"
    passwordSecretName: "my-password"
```
{% endcode %}

In this manifest, we're describing Chainlink node that connects to Ethereum Rinkeby node using `ethereumWsEndpoint`, setting ethereum chain ID using `ethereumChainId`, setting link contract address using `linkContractAddress`, connecting to postgress database instance using `databaseURL`, setting wallet password using `keystorePasswordSecretName` which accepts a name of k8s secret, setting tls configuration using `certSecretName` which accepts k8s secret name that holds `tls.key` and `tls.crt`, and finally setting API credentials using `apiCredentials`.


Apply `chainlink.yaml` manifest:

```bash
kubectl apply -f chainlink.yaml
```

Kotal operator will notice your `chainlink-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets neccessary.

You can fetch the deployed Chainlink `Node` using:

```bash
kubectl get nodes.chainlink
```

It will return an output similar to the following:

```bash
NAME             CLIENT      EthereumChainId
chainlink-node   chainlink   4
```

If you added `-o wide` it will return more info like link contract address

```bash
NAME             CLIENT      EthereumChainId   LinkContractAddress
chainlink-node   chainlink   4                 0x01BE23585060835E02B77ef475b0Cc51aA1e0709
```

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME                  READY   STATUS    RESTARTS   AGE
chainlink-node-0      1/1     Running   0          1m
```

Get the logs of the running node:

```bash
kubectl logs -f chainlink-node-0
```
