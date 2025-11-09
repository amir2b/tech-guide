# Kind

kind is a tool for running local Kubernetes clusters using Docker container “nodes”.  
kind was primarily designed for testing Kubernetes itself, but may be used for local development or CI.

## Setup

```shell
## Install Kind
## https://kind.sigs.k8s.io/docs/user/quick-start/
curl -LO https://kind.sigs.k8s.io/dl/v0.30.0/kind-linux-amd64
sudo install kind-linux-amd64 /usr/local/bin/kind
rm kind-linux-amd64
kind version

## Installing kind bash completion
kind completion bash | sudo tee /etc/bash_completion.d/kind > /dev/null

## Install KubeCtl
## https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl
kubectl version

## Installing kubectl bash completion
kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null
```

## Start

```shell
## Creates a local Kubernetes cluster using Docker container 'nodes'
kind create cluster

## Display addresses of the control plane and services
kubectl cluster-info
kubectl cluster-info --context kind-kind

## Deletes a Kind cluster from the system.
kind delete cluster
```

## References

* <https://kind.sigs.k8s.io/docs/user/quick-start/>
* <https://kubernetes.io/docs/reference/kubectl/>
