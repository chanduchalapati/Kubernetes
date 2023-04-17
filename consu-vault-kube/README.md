# Running Vault and Consul on Kubernetes

## Want to learn how to build this?

Check out the [post](https://testdriven.io/running-vault-and-consul-on-kubernetes).

## Want to use this project?

### Prerequisites

Install:

1. [Go](https://golang.org/doc/install)
1. CloudFlare's [SSL ToolKit](https://github.com/cloudflare/cfssl) (`cfssl` and `cfssljson`)
1. [Consul](https://www.consul.io/docs/install/index.html)
1. [Vault](https://www.vaultproject.io/docs/install/)
1. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)

### Minikube

Start the cluster:

```sh
$ minikube config set vm-driver hyperkit
$ minikube start
$ minikube dashboard
```

### TLS Certificates

Create a Certificate Authority:

```sh
$ cfssl gencert -initca certs/config/ca-csr.json | cfssljson -bare certs/ca
```

Create the private keys and TLS certificates:

```sh
$ cfssl gencert \
    -ca=certs/ca.pem \
    -ca-key=certs/ca-key.pem \
    -config=certs/config/ca-config.json \
    -profile=default \
    certs/config/consul-csr.json | cfssljson -bare certs/consul

$ cfssl gencert \
    -ca=certs/ca.pem \
    -ca-key=certs/ca-key.pem \
    -config=certs/config/ca-config.json \
    -profile=default \
    certs/config/vault-csr.json | cfssljson -bare certs/vault
```

### Vault and Consul

Spin up Vault and Consul on Kubernetes:

```sh
$ sh create.sh
```

### Environment Variables

In a new terminal window, navigate to the project directory and set the following environment variables:

```sh
$ export VAULT_ADDR=https://127.0.0.1:8200
$ export VAULT_CACERT="certs/ca.pem"
```

### Verify

```sh
$ kubectl get pods
$ vault status
```


Unseal Key 1: /lNxNlY0cjtuTKm7zwz5I9eb7pwtZ2psmzwKHZfZ2/c=

Initial Root Token: 5ujab08bouYT5rVB1NlK60GK

Unseal Key (will be hidden):
Key                    Value
---                    -----
Seal Type              shamir
Initialized            true
Sealed                 false
Total Shares           1
Threshold              1
Version                0.11.5
Build Date             n/a
Storage Type           n/a
Cluster Name           vault-cluster-4ce49331
Cluster ID             7b59f531-6eda-dd13-5a84-60c0b178b291
HA Enabled             true
HA Cluster             n/a
HA Mode                standby
Active Node Address    <none>
