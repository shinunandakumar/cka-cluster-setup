## Create snapshot of the etcd running at https://127.0.0.1:2379. Save snapshot into /opt/etcd-snapshot.db
    Use these are certificate for snapshot

    Ca certificate: /etc/kubernetes/pki/etcd/ca.crt
    Client certicate: /etc/kubernetes/pki/etcd/server.crt
    client key: /etc/kubernetes/pki/etcd/server.key
    and then restore from the previous ETCD backup.

    # ANS
        $ ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cert=/etc/kubernetes/pki/etcd/server.crt --cacert=/etc/kubernetes/pki/etcd/ca.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /opt/etcd-snapshot.db

## Taint the worker node to be Unschedulable. Once done, create a pod called dev-redis, image redis:alpine to ensure workloads are not scheduled to this worker node. Finally, create a new pod called prod-redis and image redis:alpine with toleration to be scheduled on node01.
    key:env_type, value:production, operator: Equal and effect:NoSchedule 

    # ANS
        $ kubectl get nodes
        $ kubectl taint node node01 env_type=production:NoSchedule
        $ kubectl describe nodes node01 | grep -i taint
        $ kubectl run dev-redis --image=redis:alpine --dyn-run=client -o yaml > pod-redis.yaml
        $ vi prod-redis.yaml
        apiVersion: v1 
        kind: Pod 
        metadata:
        name: prod-redis 
        spec:
        containers:
        - name:  prod-redis 
            image:  redis:alpine
        tolerations:
        - effect: Noschedule 
            key: env_type 
            operator: Equal 
            value: prodcution
        $ kubectl create -f prod-redis.yaml
    
## Set the node named worker node as unavailable and reschedule all the pods running on it. (Drain node)
    # ANS
        $ Kubectl drain node <worker node> --ignore-daemonsets

