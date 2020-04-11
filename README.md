**Redis**

Redis standalone with prometheus reporting.

***
redis prometheus exporter: https://github.com/oliver006/redis_exporter

***
**To create master and slaves**

    kubectl exec -n sharedservices -it redis-cluster-0 -- redis-trib create --replicas 1 \
    $(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ' -n sharedservices) 

**Add a new master**

    kubectl exec redis-cluster-0 -- redis-trib add-node 
    $(kubectl get pod redis-cluster-6 -o jsonpath='{.status.podIP}'):6379 
    $(kubectl get pod redis-cluster-0 -o jsonpath='{.status.podIP}'):6379

**Add a new slave**

    kubectl exec redis-cluster-0 -- redis-trib add-node --slave 
    $(kubectl get pod redis-cluster-7 -o jsonpath='{.status.podIP}'):6379 
    $(kubectl get pod redis-cluster-0 -o jsonpath='{.status.podIP}'):6379

**Rebalance master**

    kubectl exec redis-cluster-0 -- redis-trib rebalance --use-empty-masters 
    $(kubectl get pod redis-cluster-0 -o jsonpath='{.status.podIP}'):6379

**Add a new master**

    kubectl exec redis-cluster-0 -- redis-trib add-node \
    $(kubectl get pod redis-cluster-6 -o jsonpath='{.status.podIP}'):6379 \
    $(kubectl get pod redis-cluster-0 -o jsonpath='{.status.podIP}'):6379

**Add a new slave**

    kubectl exec redis-cluster-0 -- redis-trib add-node --slave \
    $(kubectl get pod redis-cluster-7 -o jsonpath='{.status.podIP}'):6379 \
    $(kubectl get pod redis-cluster-0 -o jsonpath='{.status.podIP}'):6379

**Rebalance master**

    kubectl exec redis-cluster-0 -- redis-trib rebalance --use-empty-masters \
    $(kubectl get pod redis-cluster-0 -o jsonpath='{.status.podIP}'):6379

#######
