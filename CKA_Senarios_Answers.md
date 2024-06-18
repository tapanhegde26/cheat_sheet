# 1.ETCD - backup and restore

## Question
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


`etcd-controlplane` pod is running in `kube-system` environment, take backup and store it in `/opt/cluster_backup.db` file.

ETCD backup is stored at the path `/opt/cluster_backup.db`  on the controlplane  node. for `--data-dir` use `/root/default.etcd` , restore it on the controlplane  node itself and , and also store restore console output store it in `restore.txt`

##  Answer
```
kubectl config use-context kubernetes-admin@kubernetes
ETCDCTL_API=3 etcdctl snapshot save /opt/cluster_backup.db   --endpoints=https://127.0.0.1:2379   --cacert=/etc/kubernetes/pki/etcd/ca.crt   --cert=/etc/kubernetes/pki/etcd/server.crt   --key=/etc/kubernetes/pki/etcd/server.key
export ETCDCTL_API=3
etcdctl --write-out=table snapshot status /opt/cluster_backup.db 
etcdctl --data-dir /root/default.etcd snapshot restore /opt/cluster_backup.db &> restore.txt
```
# 2.Secrets

## Question - 01
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


Decode the contents of the existing secret named `database-data` in the `database-ns` namespace and save the decoded content into a file located at `decoded.txt`


## Answer
```
kubectl config use-context kubernetes-admin@kubernetes
kubectl get secrets -n database-ns
kubectl get secrets -n database-ns -o yaml
echo '<value of data obtained from yaml>' | base64 --decode > decoded.txt
```
## Question - 02
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


Create a Kubernetes Secret named `database-app-secret` in the default namespace using the contents of the file `database-data.txt`

## Answer
```
kubectl create secret generic database-app-secret --from-file=database-data.txt
kubectl get secret 
kubectl get secret database-app-secret -o yaml
```

# 3. Architecture/Installation and Maintenance

## Question - 01
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


you have a script named `svc-filter.sh` . Update this script to include a command that filters and displays the value of target port of a service named `redis-service` using jsonpath only.

It should be in the format `kubectl get svc` OR It should be in the format `kubectl get service`

## Answer
```
kubectl get svc
```
svc-filter.sh script contents
```
#!/bin/bash

# Existing content of svc-filter.sh

# Add the command to filter and display the target port of redis-service
TARGET_PORT=$(kubectl get svc redis-service -o jsonpath='{.spec.ports[0].targetPort}')
echo "The target port of redis-service is: $TARGET_PORT"
```
```
chmod +x svc-filter.sh 
./svc-filter.sh
```
# 4. RBAC Authorization

## Question - 1
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


Create a service account named `app-account` , a cluster role named `app-role-cka` , and a cluster role binding named `app-role-binding-cka` . Update the permissions of this service account so that it can get the pods only in the default namespace.

## Answer
* Create service-account yaml file
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-account
  namespace: default
```
* Create cluster-role.yaml file
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: app-role-cka
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get"]
```
* Create cluster-role-binding.yaml file
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: app-role-binding-cka
subjects:
- kind: ServiceAccount
  name: app-account
  namespace: default
roleRef:
  kind: ClusterRole
  name: app-role-cka
  apiGroup: rbac.authorization.k8s.io

```
apply these changes
```
kubectl apply -f service-account.yaml
kubectl apply -f cluster-role.yaml
kubectl apply -f cluster-role-binding.yaml
```
## Question - 2
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


You have a service account named `group1-sa` , a ClusterRole named `group1-role-cka` , and a ClusterRoleBinding named `group1-role-binding-cka` . Your task is to update the permissions for the `group1-sa` service account so that it can only create , get and list the deployments and no other resources in the cluster.

## Answer
* Check all service-accounts to see if group1-sa is present
  ```
  kubectl get sa
  ```
* check all cluster-role to see if group1-role-cka is present
  ```
  kubectl get clusterrole
  ```
* check all cluster-role-binding to see if group1-role-binding-cka is present
  ```
  kubectl get clusterrolebinding
  ```
* Next update cluster-role as below and save it in separate yaml file (updated-cluster-role.yaml)
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: group1-role-cka
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["create", "get", "list"]
```
* apply updated-cluster-role.yaml file to update changes
```
kubectl apply -f updated-cluster-role.yaml
```
# 5. Pod resource

## Question - 01
Find the pod that consumes the most CPU in all namespace(including kube-system) in all cluster(currently we have single cluster). Then, store the result in the file `high_cpu_pod.txt` with the following format: pod_name,namespace .
## Answer
```
kubectl top pods --all-namespaces --sort-by=cpu | awk 'NR==2 {print $2","$1}' > high_cpu_pod.txt
```
To get details of node which is consuming more resources
```
echo "$(kubectl config current-context),$(kubectl top nodes --no-headers | sort -nrk 3 | head -n 1 | awk '{print $1}')" > high_memory_node.txt
```

# 6. Service and Networking

## Question - 01
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


You have an existing Nginx pod named `nginx-pod` . Perform the following steps:

Expose the `nginx-pod` internally within the cluster using a Service named `nginx-service` .
Use port forwarding to service to access the Welcome content of `nginx-pod` using the curl command.

## Answer
* Create service named nginx-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-pod
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```
command : 
```
kubectl apply -f nginx-service.yaml
```
* use port forwarding to access the pod directly.
```
kubectl port-forward pod/nginx-pod 8080:80
```

## Question - 02
For this question, please set this context (In exam, diff cluster name)

`kubectl config use-context kubernetes-admin@kubernetes`


Part I:

Create a Kubernetes ClusterIP service named `nginx-service` . This service should expose to `nginx-deployment` , using port 8080 and target port 80
Part II:

Retrieve and store the IP addresses of the pods. Sort the output by their IP addresses in Ascending order and save it to the file pod_ips.txt in the following format:
`IP_ADDRESS
127.0.0.1
127.0.0.2
127.0.0.3`

## Answer
* Create nginx-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
```
```
kubectl apply -f nginx-service.yaml
```
* Retrieve and store the IP addresses of the pods. Sort the output by their IP addresses in Ascending order and save it to the file pod_ips.txt
```
kubectl get pods -o wide --no-headers | awk '{print $6}' | tr " " "\n" | sort -n > pod_ips.txt
```
