# kubernetes_cheat_sheet
cheat sheet for kubectl commands

## 1. Kubectl Alias
Linux
```
alias k=kubectl
```
Windows
```
Set-Alias -Name k -Value kubectl
```
## 2. Cluster Info
Get clusters
```
kubectl config get-clusters
```
Get cluster info.
```
kubectl cluster-info
```
## 3. Contexts
A context is a cluster, namespace and user.

Get a list of contexts.
```
kubectl config get-contexts
```
Get the current context.
```
kubectl config current-context
```
Switch current context.
```
kubectl config use-context docker-desktop
```
Set default namesapce
```
kubectl config set-context $(kubectl config current-context) --namespace=my-namespace
```
To switch between contexts, you can also install and use [kubectx](https://github.com/ahmetb/kubectx).

## 4. Get Commands
```
kubectl get all
kubectl get namespaces
kubectl get configmaps
kubectl get nodes
kubectl get pods
kubectl get rs
kubectl get svc <service-name>
kubectl get endpoints <service-name>
```
Additional switches that can be added to the above commands:

* `-o wide` - Show more information.
* `--watch` or `-w` `- watch` for changes.

## 5. Namespaces
To list all pods in a namespace
```
kubectl get po -n <namespace_name>
Eg : kubectl get po -n dev
```
Get current context and set namespace as a default for context
```
kubectl config set-context $(kubectl config current-context) -n dev
```
To list all pods in all namespaces
```
kubectl get pods --all-namespaces
```
To switch namespaces, you can also install and use [kubens](https://github.com/ahmetb/kubectx/blob/master/kubens).

## Uses of kubectl-run command
Create an NGINX Pod
```
kubectl run nginx --image=nginx
```

Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)

```
kubectl run nginx --image=nginx --dry-run=client -o yaml
```

Create a deployment
```
kubectl create deployment --image=nginx nginx
```

Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
```
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
```

Generate Deployment YAML file (-o yaml). Don’t create it(–dry-run) and save it to a file.
```
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
```

Make necessary changes to the file (for example, adding more replicas) and then create the deployment.

kubectl create -f nginx-deployment.yaml



OR

In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.
```
kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
```
