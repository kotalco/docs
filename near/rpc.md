## Deploy NEAR RPC Node

The following manifest describes a NEAR node that syncs NEAR mainnet `network: mainnet`, enables HTTP JSON-RPC server `rpc: true` and uses [NEAR Core](https://github.com/near/nearcore) client:

{% code title="near.yaml" %}
```yaml
apiVersion: near.kotal.io/v1alpha1
kind: Node
metadata:
  name: near-node
spec:
  network: mainnet
  rpc: true
```
{% endcode %}

Apply `near.yaml` manifest:

```bash
kubectl apply -f near.yaml
```

Kotal operator will notice your `near-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets neccessary.

You can fetch the deployed NEAR `Node` using:

```bash
kubectl get nodes.near
```

It will return an output similar to the following:

```bash
NAME         NETWORK    CLIENT      VALIDATOR
near-node    mainnet    nearcore    fasle
```

## Fetch Node Logs

Get the pods that has been created by Kotal for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME              READY   STATUS    RESTARTS   AGE
near-node-0       1/1     Running   0          1m
```

Get the logs of the running node:

```bash
kubectl logs -f near-node-0
```

It will return node logs similar to the following:

```
Apr 07 12:18:33.280  INFO neard: Version: 1.23.1, Build: 1.23.0-9-gc0551c84b-modified, Latest Protocol: 50
Apr 07 12:18:33.287  INFO near: Did not find "/home/near/kotal-data/data" path, will be creating new store database
Apr 07 12:18:33.806  INFO stats: Server listening at ed25519:4WdX3qM4qqWG4DmD1eNp5LfMGu3N2HtoQHZArVjdQpGf@0.0.0.0:24567
Apr 07 12:18:43.816  INFO stats: # 9820210 Downloading headers 0.00% (53203932)   5/3/40 peers ⬇ 7.2kiB/s ⬆ 0.4kiB/s 0.00 bps 0 gas/s CPU: 0%, Mem: 0 B
Apr 07 12:18:53.819  INFO stats: # 9820210 Downloading headers 0.00% (53203933)   5/3/40 peers ⬇ 7.2kiB/s ⬆ 0.5kiB/s 0.00 bps 0 gas/s CPU: 10%, Mem: 134.4 MiB
Apr 07 12:19:03.787  INFO stats: # 9820210 Downloading headers 0.00% (53203933)   5/3/40 peers ⬇ 7.2kiB/s ⬆ 0.5kiB/s 0.00 bps 0 gas/s CPU: 24%, Mem: 165.9 MiB
Apr 07 12:19:13.790  INFO stats: # 9820210 Downloading headers 0.00% (53203933)   5/3/40 peers ⬇ 7.2kiB/s ⬆ 0.5kiB/s 0.00 bps 0 gas/s CPU: 9%, Mem: 175.0 MiB
...
```

## Call JSON-RPC Method

By default, NEAR node JSON-RPC server port is `3030`, which cane be changed using `rpcPort`.

Forward localhost:3030 calls to the node pod:

```bash
kubectl port-forward near-node-0 3030
```

In another terminal window call `status` JSON-RPC method to get the general status of a given node (sync status, nearcore node version, protocol version) and the current set of validators:

```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "status"}' 0.0.0.0:3030
```

You will get JSON result similar to the following:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "version": {
      "version": "1.23.1",
      "build": "1.23.0-9-gc0551c84b-modified"
    },
    "chain_id": "mainnet",
    "protocol_version": 29,
    "latest_protocol_version": 50,
    "rpc_addr": "0.0.0.0:3030",
    "validators": [
      {
        "account_id": "nfvalidator2.near",
        "is_slashed": false
      },
      {
        "account_id": "nfvalidator1.near",
        "is_slashed": false
      },
      {
        "account_id": "nfvalidator4.near",
        "is_slashed": false
      },
      {
        "account_id": "nfvalidator3.near",
        "is_slashed": false
      }
    ],
    "sync_info": {
      "latest_block_hash": "EPnLgE7iEq9s7yTkos96M3cWymH5avBAPm3qx3NXqR8H",
      "latest_block_height": 9820210,
      "latest_state_root": "5o1trt9N9YgDL4xeY33z9adS5Ub3jMmuyQpWM7UHowrq",
      "latest_block_time": "2020-07-21T16:55:51.591948Z",
      "syncing": true,
      "earliest_block_hash": "EPnLgE7iEq9s7yTkos96M3cWymH5avBAPm3qx3NXqR8H",
      "earliest_block_height": 9820210,
      "earliest_block_time": "2020-07-21T16:55:51.591948Z"
    },
    "validator_account_id": "validator"
  },
  "id": 53
}
```

Finally you can delete the node by:

```bash
kubectl delete -f near.yaml
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal NEAR `Node` controller.