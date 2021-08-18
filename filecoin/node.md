{% hint style="warning" %}
Kotal support for Filecoin is still very early, so we're not exposing lots of management and integration features.
{% endhint %}

## Deploy Node

{% code title="nerpa.yaml" %}
```yaml
apiVersion: filecoin.kotal.io/v1alpha1
kind: Node
metadata:
  name: nerpa-node
spec:
  network: nerpa
```
{% endcode %}

This is a simple filecoin `Node` that joins nerpa test network.

Let's deloy the node:

```bash
kubectl apply -f nerpa.yaml
```

Kotal operator will notice your `nerpa-node` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets.

```bash
kubectl get peers
```

It will return an output similar to the following:

```bash
NAME          NETWORK   CLIENT
nerpa-node    nerpa     lotus
```

## Fetch Node Logs

Get the pods created for the node:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME            READY   STATUS    RESTARTS   AGE
nerpa-node-0    1/1     Running   0          5m
```

{% hint style="info" %}
If pod `STATUS` is `Pending`, most probably it's due to not enough cpu and memory, because Kotal allocates lots of cpu cores and memory for public nodes. The required node resources can be changed using `spec.resources.cpu`, `spec.resources.memory`, and `spec.resources.storage`.
{% endhint %}

Check the logs of the running node:

```bash
kubectl logs -f nerpa-node-0
```

Finally you can delete the filecoin node and all its resources by

```bash
kubectl delete -f nerpa.yaml
```

and kubernetes garbage collector will delete all resources created by the node controller.
