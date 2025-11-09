# Kubectl

Kubectl is a command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.

## Setup

```shell
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

## Commands

```shell
kubectl get nodes

kubectl get all
kubectl get pod -A

kubectl apply -f file.yaml

## Pod
kubectl run nginx-pod --image=nginx:alpine
kubectl get pod
kubectl describe pod nginx-pod
kubectl logs nginx-pod -f
kubectl exec -it nginx-pod -- sh
kubectl port-forward pod/nginx-pod 8080:80
kubectl delete pod nginx-pod

## Deployment
kubectl create deployment nginx-deployment --image=nginx:alpine
kubectl get deployment
kubectl edit deployment nginx-deployment
KUBE_EDITOR="nano" kubectl edit deployment nginx-deployment
kubectl describe deployment nginx-deployment
kubectl logs deployments/nginx-deployment
kubectl exec -it deployments/nginx-deployment -- sh
kubectl delete deployment nginx-deployment

## Remove terminating pods
kubectl get pods --all-namespaces | grep Terminating | while read line; do pod_name=$(echo $line | awk '{print $2}' ) name_space=$(echo $line | awk '{print $1}' ); kubectl delete pods $pod_name -n $name_space --grace-period=0 --force; done

## Remove terminating namespace
(
NAMESPACE=argocd
kubectl proxy &
kubectl get namespace $NAMESPACE -o json |jq '.spec = {"finalizers":[]}' >temp.json
curl -k -H "Content-Type: application/json" -X PUT --data-binary @temp.json 127.0.0.1:8001/api/v1/namespaces/$NAMESPACE/finalize
)
```

## References

* <https://kubernetes.io/docs/reference/kubectl/>
