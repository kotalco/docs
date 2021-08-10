## Requirements

- Kubernetes cluster
- Cert Manager

### Kubernetes cluster

For development and testing purposes we recommend using [kind](https://kind.sigs.k8s.io/) (Kubernetes in Docker), it's an easy way to create and tear down kubernetes clusters in seconds.
For staging and production purposes you can use any public cloud kubernetes as a service like [Amazon EKS](https://aws.amazon.com/eks/), [Azure AKS](https://azure.microsoft.com/en-us/services/kubernetes-service/) or [Google GKE](https://cloud.google.com/kubernetes-engine).

kind installation guide can be found [here](https://kind.sigs.k8s.io/docs/user/quick-start/).

### Cert Manager

Cert Manager is a native Kubernetes certificate management controller. It can help with issuing certificates from a variety of sources, such as [Let’s Encrypt](https://letsencrypt.org), [HashiCorp Vault](https://www.vaultproject.io/), a simple signing key pair, or self-signed. It will ensure certificates are valid and up to date, and attempt to renew certificates at a configured time before expiry.

Kotal **webhook** component uses cert-manager for issuing certificates to default and validate your Blockchain resources such as nodes, beacon nodes, ipfs peers ... etc.

cert-manager installation guide can be found [here](https://cert-manager.io/docs/installation/).

## Install kotal

The latest version of Kotal operator `v0.1-alpha.4` can be installed into your Kubernetes cluster simply by:

```bash
kubectl apply -f https://github.com/kotalco/kotal/releases/download/v0.1-alpha.4/kotal.yaml
```
