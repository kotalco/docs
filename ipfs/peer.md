## Deploy Peer

{% code title="peer.yaml" %}
```yaml
apiVersion: ipfs.kotal.io/v1alpha1
kind: Peer
metadata:
  name: simple-peer
spec: {}
```
{% endcode %}

This is a simple ipfs peer that joins the public ipfs swarm, and will starts with the all default settings that's similar to running `ipfs init` then `ipfs daemon` on your machine.

Let's deploy the peer:

```bash
kubectl apply -f peer.yaml
```

Kotal operator will notice your `simple-peer` and will create all the necessary pods, persistent volumes, services, configmaps, and secrets.

```bash
kubectl get peers
```

It will return an output similar to the following:

```bash
NAME          CLIENT
simple-peer   go-ipfs
```

## Fetch Peer Logs

Get the pods created for the peer:

```bash
kubectl get pods
```

It will return an output similar to the following:

```bash
NAME            READY   STATUS    RESTARTS   AGE
simple-peer-0   1/1     Running   0          5m
```

Check the logs of the running peer:

```bash
kubectl logs -f simple-peer-0
```

You'll get output similar to the following:

```text
Initializing daemon...
go-ipfs version: 0.8.0-ce693d7
Repo version: 11
System version: amd64/linux
Golang version: go1.14.4
Swarm listening on /ip4/10.244.0.6/tcp/4001
Swarm listening on /ip4/10.244.0.6/udp/4001/quic
Swarm listening on /ip4/127.0.0.1/tcp/4001
Swarm listening on /ip4/127.0.0.1/udp/4001/quic
Swarm listening on /ip6/::1/tcp/4001
Swarm listening on /ip6/::1/udp/4001/quic
Swarm listening on /p2p-circuit
Swarm announcing /ip4/10.244.0.6/tcp/4001
Swarm announcing /ip4/10.244.0.6/udp/4001/quic
Swarm announcing /ip4/127.0.0.1/tcp/4001
Swarm announcing /ip4/127.0.0.1/udp/4001/quic
Swarm announcing /ip6/::1/tcp/4001
Swarm announcing /ip6/::1/udp/4001/quic
API server listening on /ip4/0.0.0.0/tcp/5001
WebUI: http://0.0.0.0:5001/webui
Gateway (readonly) server listening on /ip4/0.0.0.0/tcp/8080
Daemon is ready
```

Your peer is up and running and ready to receive api calls.

## Read file Using IPFS HTTP API

Let's forward localhost:5001 calls to simple-peer:5001

```bash
kubectl port-forward simple-peer-0 5001
```

In another terminal window, send http api call using cURL:

```bash
curl -X POST "http://127.0.0.1:5001/api/v0/cat?arg=QmQPeNsJPyVWPFDVHb77w8G42Fvo15z4bG2X8D2GhfbSXc/readme"
```

You'll get the following output:

```txt
Hello and Welcome to IPFS!

██╗██████╗ ███████╗███████╗
██║██╔══██╗██╔════╝██╔════╝
██║██████╔╝█████╗  ███████╗
██║██╔═══╝ ██╔══╝  ╚════██║
██║██║     ██║     ███████║
╚═╝╚═╝     ╚═╝     ╚══════╝

If you're seeing this, you have successfully installed
IPFS and are now interfacing with the ipfs merkledag!

 -------------------------------------------------------
| Warning:                                              |
|   This is alpha software. Use at your own discretion! |
|   Much is missing or lacking polish. There are bugs.  |
|   Not yet secure. Read the security notes for more.   |
 -------------------------------------------------------

Check out some of the other files in this directory:

  ./about
  ./help
  ./quick-start     <-- usage examples
  ./readme          <-- this file
  ./security-notes
```

You can also view ipfs webui by visiting http://0.0.0.0:5001/webui

Finally, delete the ipfs peer:

```bash
kubectl delete -f peer.yaml
```

Kubernetes garbage collector will delete all the resources that has been created by Kotal IPFS `Peer` controller.
