IPFS is a distributed system for storing and accessing files, websites, applications, and data.

We've extended Kubernetes with `Peer` and `ClusterPeer` custom resources which can be used to deploy IPFS peers, swarms, and clusters from the given spec.

## Peer

`Peer` is an ipfs peer running `go-ipfs` client and connecting to the public ipfs swarm or a private swarm secured by swarm key. Here's an example of a very basic ipfs peer connecting to the public ipfs swarm.

```yaml
apiVersion: ipfs.kotal.io/v1alpha1
kind: Peer
metadata:
  Name: simple-peer
spec: {}
```

For all the fields associated with the `Peer` API resource:

```bash
kubectl explain peers --api-version ipfs.kotal.io/v1alpha1
kubectl explain peers.spec --api-version ipfs.kotal.io/v1alpha1
```

Full ipfs peer reference is documented [here](../reference/ipfs.md).

## ClusterPeer

`ClusterPeer` is an ipfs cluster peer running `ipfs-cluster-service` client, connecting to ipfs peer and optional bootstrap cluster peer(s). `ClusterPeer`s provide data orchestration across a swarm of IPFS daemons by allocating, replicating and tracking a global pinset distributed among multiple peers. Here's an example of cluster peer connecting to ipfs peer.

```yaml
apiVersion: ipfs.kotal.io/v1alpha1
kind: ClusterPeer
metadata:
  Name: simple-cluster-peer
spec:
  peerEndpoint: /dns4/simple-peer/tcp/5001
```

For all the fields associated with the `ClusterPeer` API resource:

```bash
kubectl explain clusterpeers --api-version ipfs.kotal.io/v1alpha1
kubectl explain clusterpeers.spec --api-version ipfs.kotal.io/v1alpha1
```

Full ipfs cluster peer reference is documented [here](../reference/ipfs.md).
