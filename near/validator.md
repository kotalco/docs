
In this tutorial, we will learn how to deploy a NEAR validator node, and load a validator key into it.

## Generate Validator Key

Generate validator account using [NEAR CLI](https://github.com/near/near-cli) tool. Which can be installed using:

```bash
npm install -g near-cli
```

{% hint style="warning" %}
Validator account must have the name `validator.`
{% endhint %}

```bash
near generate-key validator
```

An ed25519 private key will be generated and saved in current user home directory under `.near-credentials/testnet`

```bash
cat ~/.near-credentials/testnet/validator.json
```

It will return an output similar to the following

```json
{
  "account_id": "validator",
  "public_key": "ed25519:CdTBLXMmu9gzoCbNKdUnej1tJNMz5tzRjJF2a9DwAgUr",
  "private_key": "ed25519:4k83DwbSpD3zzai4ZPdeRJcfXttU3Uq68mWWhni6ra2RKnG3jyVKEZyP14gDJZ9W1oqFujpAkidoNrYY4TLqijsG"
}
```

{% hint style="warning" %}
Change `private_key` field to `secret_key`.
{% endhint %}

## Generate Secret for Validator Private Key

Create a Kubernetes secret from the generated private key in the previous step:

```bash
kubectl create secret generic validator-key  --from-file=key=$HOME/.near-credentials/testnet/validator.json
```

## Deploy NEAR Validator Node

The following manifest describes a NEAR node that syncs NEAR testnet `network: testnet`, enables HTTP JSON-RPC server `rpc: true`, loads validator key from the Kubernetes secret that we've created in the previous step, and uses [NEAR Core](https://github.com/near/nearcore) client:

{% code title="near.yaml" %}
```yaml
apiVersion: near.kotal.io/v1alpha1
kind: Node
metadata:
  name: near-node
spec:
  network: testnet
  validatorSecretName: validator-key
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
near-node    testnet    nearcore    true
```

Note that validator is true in the prevous output 😇

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
Apr 08 12:24:09.930  INFO neard: Version: 1.23.1, Build: 1.23.0-9-gc0551c84b-modified, Latest Protocol: 50
Apr 08 12:24:09.937  INFO near: Did not find "/home/near/kotal-data/data" path, will be creating new store database
Apr 08 12:24:10.438  INFO stats: Server listening at ed25519:GK2bMCbzgjFrdAQPFu2qSc6rViwX4MUb8WZb97CAfUMr@0.0.0.0:24567
Apr 08 12:24:20.444  INFO stats: # 9820210 Downloading headers 0.00% (53273496)   5/3/40 peers ⬇ 0.7kiB/s ⬆ 0.2kiB/s 0.00 bps 0 gas/s CPU: 0%, Mem: 0 B
Apr 08 12:24:30.447  INFO stats: # 9820210 Downloading headers 0.00% (53273498)   5/3/40 peers ⬇ 0.7kiB/s ⬆ 0.2kiB/s 0.00 bps 0 gas/s CPU: 4%, Mem: 78.4 MiB
Apr 08 12:24:40.417  INFO stats: # 9820210 Downloading headers 0.00% (53273497)   5/3/40 peers ⬇ 0.7kiB/s ⬆ 0.2kiB/s 0.00 bps 0 gas/s CPU: 10%, Mem: 99.6 MiB
...
```


Finally you can delete the node by:

```bash
kubectl delete -f near.yaml
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal NEAR `Node` controller.

## Further Reading

1. [Deploy NEAR stacking pool](https://near-nodes.io/validator/deploy-on-mainnet)
2. [Staking on NEAR](https://near-nodes.io/validator/staking-and-delegation)
3. [Upgradable staking pool](https://near-nodes.io/validator/staking-pool-migration)
4. [NEAR validator bootcamp](https://near-nodes.io/validator/validator-bootcamp)

