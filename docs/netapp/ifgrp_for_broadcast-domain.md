## 查看failover-groups 
```
cluster-xlh::> failover-groups show
  (network interface failover-groups show)
                                  Failover
Vserver          Group            Targets
---------------- ---------------- --------------------------------------------
Cluster
                 Cluster
                                  cluster-xlh-01:e0a, cluster-xlh-01:e0f,
                                  cluster-xlh-02:e0a, cluster-xlh-02:e0f
cluster-xlh
                 Default
                                  cluster-xlh-01:e0M, cluster-xlh-01:e0e,
                                  cluster-xlh-01:e0j, cluster-xlh-01:e4a,
                                  cluster-xlh-01:e4e, cluster-xlh-02:e0M,
                                  cluster-xlh-02:e0e, cluster-xlh-02:e0j,
                                  cluster-xlh-02:e4a, cluster-xlh-02:e4e
```

## remove port from broadcast-domain 
```
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-01:e0e
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-01:e0j
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-01:e4e
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-01:e0a
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-02:e0e
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-02:e0j
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-02:e4e
cluster-xlh::> broadcast-domain remove-ports -broadcast-domain Default -ports cluster-xlh-02:e0a

cluster-xlh::> failover-groups show
  (network interface failover-groups show)
                                  Failover
Vserver          Group            Targets
---------------- ---------------- --------------------------------------------
Cluster
                 Cluster
                                  cluster-xlh-01:e0a, cluster-xlh-01:e0f,
                                  cluster-xlh-02:e0a, cluster-xlh-02:e0f
cluster-xlh
                 Default
                                  cluster-xlh-01:e0M, cluster-xlh-02:e0M
```

## create new broadcast-domain 
```
cluster-xlh::> broadcast-domain create -broadcast-domain data -mtu 1500
cluster-xlh::> broadcast-domain show
  (network port broadcast-domain show)
IPspace Broadcast                                         Update
Name    Domain Name    MTU  Port List                     Status Details
------- ----------- ------  ----------------------------- --------------
Cluster Cluster       9000
                            cluster-xlh-01:e0a            complete
                            cluster-xlh-01:e0f            complete
                            cluster-xlh-02:e0a            complete
                            cluster-xlh-02:e0f            complete
Default Default       1500
                            cluster-xlh-01:e0M            complete
                            cluster-xlh-02:e0M            complete
        data          1500
```

## add port to broadcast-domain 
```
cluster-xlh::> broadcast-domain add-ports -broadcast-domain data -ports cluster-xlh-01:e4e
cluster-xlh::> broadcast-domain add-ports -broadcast-domain data -ports cluster-xlh-02:e4e

cluster-xlh::> failover-groups show
  (network interface failover-groups show)
                                  Failover
Vserver          Group            Targets
---------------- ---------------- --------------------------------------------
Cluster
                 Cluster
                                  cluster-xlh-01:e0a, cluster-xlh-01:e0f,
                                  cluster-xlh-02:e0a, cluster-xlh-02:e0f
cluster-xlh
                 Default
                                  cluster-xlh-01:e0M, cluster-xlh-02:e0M
                 data
                                  cluster-xlh-01:e4e, cluster-xlh-02:e4e
```