# ETCD - backup and restore

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
