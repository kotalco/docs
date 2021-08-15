> Staking is the act of depositing 32 ETH to activate validator software. As a validator you’ll be responsible for storing data, processing transactions, and adding new blocks to the blockchain. This will keep Ethereum secure for everyone and earn you new ETH in the process. This process, known as proof-of-stake, is being introduced by the Beacon Chain.
> [source](https://ethereum.org/en/eth2/staking/)

## Deploy Validator Client

{% code title="validator.yaml" %}
```yaml
apiVersion: ethereum2.kotal.io/v1alpha1
kind: Validator
metadata:
  name: teku-validator
spec:
  client: teku
  network: pyrmont
  beaconEndpoints:
    - http://teku-beacon-node:9999
  graffiti: Validated by Kotal
  keystores:
    - secretName: my-validator
```
{% endcode %}

In this validator client, we're using ConsenSys Teku Ethereum 2.0 client `client: teku`,  validating pyrmont network blockchain `network: pyrmont`, connecting to beacon node endpoint using `beaconEndpoints: ...`, setting the graffiti text to include in the propsed blocks `graffiti: validated by Kotal`, and loading a validator keystore using `keystores: ...` from a kubernetes secret called `my-validator`.

{% hint style="warning" %}
The keystores loaded using `spec.keystores[].secretName` from kubernetes secret, must contain two data keys:
* `keystore` key hodling the BLS12-381 keystore JSON file
* `password` key holding the encryption key of the keystore.
{% endhint %}

Let's deploy the validator client:

```bash
kubectl apply -f validator.yaml
```

Kotal operator will notice your `teku-validator` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets.

You can fetch the deployed Ethereum 2.0 `Validator` using:

```bash
kubectl get validators
```

It will return an output similar to the following:

```bash
NAME             CLIENT   NETWORK   AGE
teku-validator   teku     pyrmont   1m
```

## Fetch Validator Logs

Get the pods created for the validator client:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME                 READY   STATUS    RESTARTS   AGE
teku-validator-0     1/1     Running   0          1m
```

Get the logs of the running validator pod:

```bash
kubectl logs -f teku-validator-0
```

You'll get an output similar to the following:

```
...
21:21:30.668 INFO  - Storing validator data in: /opt/teku/kotal-data/validator
21:21:30.751 INFO  - Loading 1 validator keys...
21:21:31.759 INFO  - Loaded 1 Validators: 83dbb18
...
```

Finally delete the validator client:

```bash
kubectl delete validator teku-validator
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal Ethereum 2.0 `Validator` controller.
