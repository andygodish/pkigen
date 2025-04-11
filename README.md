# pkigen

Generate PKI certificates and keys using Kubernetes and Cert-Manager.

## k3d Cluster

### PRerequisites

- [pre-requisites](https://k3d.io/stable/#requirements)
- [UDS CLI](https://uds.defenseunicorns.com/getting-started/basic-requirements/)

---

For quick access to a Kubernetes cluster, you can use `k3d` as part of a `UDS bundle.` Navigate to bundles/k3d to see the structure of the bundle. 

The k3d bundle contains a single [Zarf package reference](https://github.com/defenseunicorns/uds-k3d). This package deploys a single-node k3d cluster on your local machine along with an ansilary set of tools that allow you to interact with services deployed to the cluster.