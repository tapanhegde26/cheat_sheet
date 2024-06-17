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
