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

## Enable kind autocompletion 
kind completion bash | sudo tee /etc/bash_completion.d/kind > /dev/null
sudo chmod a+r /etc/bash_completion.d/kind

## Install Kubectl
## https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl
kubectl version

## Enable kubectl autocompletion 
kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null
sudo chmod a+r /etc/bash_completion.d/kubectl
```

## Start and End

```shell
## Creates a local Kubernetes cluster using Docker container 'nodes'
kind create cluster

## Display addresses of the control plane and services
kubectl cluster-info
kubectl cluster-info --context kind-kind

## Deletes a Kind cluster from the system.
kind delete cluster
```

## Commands

```shell
## Loading an Image Into Your Cluster
kind load docker-image my-app:latest

## Configuring Your kind Cluster (explain below)
kind create cluster --config kind-example-config.yaml

docker exec -it kind-control-plane bash

## Get a list of images present on a cluster node
docker exec kind-control-plane crictl images

## Exporting Cluster Logs
kind export logs

## Help provides help for any command in the application.
kind help

## Prints the kind CLI version
kind version
```

### Configuring Your kind Cluster

<https://kind.sigs.k8s.io/docs/user/configuration/>

```yaml
## three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker

## a cluster with 3 control-plane nodes and 3 workers
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker

## Mapping ports to the host machine
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    listenAddress: "0.0.0.0" # Optional, defaults to "0.0.0.0"
    protocol: udp # Optional, defaults to tcp
```

### Ingress NGINX

```shell
kind create cluster --config kind-ingress-config.yaml

kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s

kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/usage.yaml

# should output "foo-app"
curl localhost/foo

# should output "bar-app"
curl localhost/bar
```

## References

* <https://kind.sigs.k8s.io/docs/user/quick-start/>
* <https://kubernetes.io/docs/reference/kubectl/>
