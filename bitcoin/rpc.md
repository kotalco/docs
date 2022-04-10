## Generate JSON-RPC password secret

Bitcoin nodes that enable JSON-RPC server are required to use basic authentication while calling JSON-RPC methods. 

{% code title="rpc-user-password.yaml" %}
```yaml
# WARNING: DON'T use the following secret in production
apiVersion: v1
kind: Secret
metadata:
  name: kotal-rpc-user-password
stringData:
  password: s3cr3t
```
{% endcode %}

Apply `rpc-user-password.yaml` to create the password secret:

```bash
kubectl apply -f rpc-user-password.yaml
```

## Deploy Bitcoin RPC Node

The following manifest describes a Bitcoin node that syncs Bitcoin mainnet `network: mainnet`, enables HTTP JSON-RPC server `rpc: true`, defines JSON-RPC user credentials `rpcUsers: ...` and uses [Bitcoin Core](https://github.com/bitcoin/bitcoin) client:

{% code title="bitcoin.yaml" %}
```yaml
apiVersion: bitcoin.kotal.io/v1alpha1
kind: Node
metadata:
  name: bitcoin-node
spec:
  network: mainnet
  rpc: true
  rpcUsers:
    - username: kotal
      passwordSecretName: kotal-rpc-user-password
```
{% endcode %}

Apply `bitcoin.yaml` manifest:

```bash
kubectl apply -f bitcoin.yaml
```

Kotal operator will notice your `bitcoin-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets neccessary.

You can fetch the deployed Bitcoin `Node` using:

```bash
kubectl get nodes.bitcoin
```

It will return an output similar to the following:

```bash
NAME            NETWORK    CLIENT
bitcoin-node    mainnet    bitcoincore
```

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME              READY   STATUS    RESTARTS   AGE
bitcoin-node-0    1/1     Running   0          1m
```

Get the logs of the running node:

```bash
kubectl logs -f bitcoin-node-0
```

It will return node logs similar to the following:

```bash
2022-04-09T18:26:45Z Bitcoin Core version v22.0.0 (release build)
2022-04-09T18:26:45Z InitParameterInteraction: parameter interaction: -bind set -> setting -listen=1
2022-04-09T18:26:45Z Assuming ancestors of block 00000000000000000008a89e854d57e5667df88f1cdef6fde2fbca1de5b639ad have valid signatures.
2022-04-09T18:26:45Z Setting nMinimumChainWork=00000000000000000000000000000000000000001fa4663bbbe19f82de910280
2022-04-09T18:26:45Z Using the 'sse4(1way),sse41(4way),avx2(8way)' SHA256 implementation
2022-04-09T18:26:45Z Using RdRand as an additional entropy source
2022-04-09T18:26:45Z Wallet disabled!
...
2022-04-09T18:27:02Z Synchronizing blockheaders, height: 9999 (~1.44%)
2022-04-09T18:27:02Z Synchronizing blockheaders, height: 11999 (~1.73%)
2022-04-09T18:27:02Z Synchronizing blockheaders, height: 13999 (~2.02%)
2022-04-09T18:27:02Z Synchronizing blockheaders, height: 15999 (~2.31%)
2022-04-09T18:27:02Z Synchronizing blockheaders, height: 17999 (~2.61%)
2022-04-09T18:27:03Z Synchronizing blockheaders, height: 19999 (~2.90%)
2022-04-09T18:27:03Z Synchronizing blockheaders, height: 21999 (~3.21%)
2022-04-09T18:27:03Z Synchronizing blockheaders, height: 23999 (~3.51%)
2022-04-09T18:27:04Z Synchronizing blockheaders, height: 25999 (~3.82%)
2022-04-09T18:27:04Z Synchronizing blockheaders, height: 27999 (~4.13%)
2022-04-09T18:27:04Z Synchronizing blockheaders, height: 29999 (~4.43%)
...
```

## Call JSON-RPC Method

By default, Bitcoin node JSON-RPC server port is `8332`, which can be changed using `rpcPort`.

Forward localhost:8332 calls to the node pod:

```bash
kubectl port-forward bitcoin-node-0 8332
```

In another terminal window call `getblockhash` JSON-RPC method to get the genesis block hash:

```bash
curl --user "kotal:s3cr3t" --data-binary '{"jsonrpc": "1.0", "id": 1, "method": "getblockhash", "params": [0]}' -H 'content-type: text/plain;' http://0.0.0.0:8332
```

You will get JSON result similar to the following:

```json
{
  "result":"000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f",
  "error":null,
  "id":1
}
```

Finally you can delete the node by:

```bash
kubectl delete -f bitcoin.yaml
```

Kubernetes garbage collector will delete all the resources that has been created by Bitcoin `Node` controller.
