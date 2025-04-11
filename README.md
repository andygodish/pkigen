# pkigen

Generate PKI certificates and keys using Kubernetes and Cert-Manager.

## k3d Cluster

### PRerequisites

- [pre-requisites](https://k3d.io/stable/#requirements)
- [UDS CLI](https://uds.defenseunicorns.com/getting-started/basic-requirements/)

---

For quick access to a Kubernetes cluster, you can use `k3d` as part of a `UDS bundle.` Navigate to bundles/k3d to see the structure of the bundle. 

The k3d bundle contains a single [Zarf package reference](https://github.com/defenseunicorns/uds-k3d). This package deploys a single-node k3d cluster on your local machine along with an ansilary set of tools that allow you to interact with services deployed to the cluster.

## Cert-Manager Zarf Package

The upstream cert-manager helm chart is compiled locally as a Zarf package and deployed to the k3d cluster as part of the cert-manager bundle found in the `bundles` directory. 

### Components

The cert-manager Zarf package pulls down the upstream helm chart and then creates additional custom resrouces used to generate your own PKI. 

When creating PKI infrastructure with Cert-Manager, a circular relationship is created. Here's how the process works step by step:

First, create the `selfsigned-issuer`:

- This is a special type of issuer that can create self-signed certificates. 
- It doesn't depend on any existing certificates

Then use the selfsigned-issuer to create the root CA certificate:

- The root certificate is, by definition, self-signed
- This creates both a certificate and its corresponding private key and stores them in a k8s tls secret type

Next, the root-ca-issuer that references the root CA certificate:

- This creates an issuer that can sign new certificates using the root CA's private key
- The issuer needs to reference the secret containing the root CA certificate and private ke

Use root-ca-issuer to create the intermediate CA certificate:

- This certificate is signed by the root CA
- This creates both an intermediate certificate and its corresponding private key


Create intermediate-ca-issuer that references the intermediate CA certificate:

- This creates an issuer that can sign new certificates using the intermediate CA's private key
- The issuer needs to reference the secret containing the intermediate CA certificate and private key

Use intermediate-ca-issuer to create service certificates:

- These certificates are signed by the intermediate CA
- These are your actual TLS certificates used by services

In this flow, we're creating both certificates AND issuers in alternating steps. Each certificate (except the last "leaf") becomes the basis for a new issuer.