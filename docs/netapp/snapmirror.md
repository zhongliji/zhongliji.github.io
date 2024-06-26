## Major setup:
```
1. Create intercluster lif on each cluster nodes with cluster vserver name.
2. Create cluster peer on source cluster and destination cluster with intercluster lif.
3. Create vserver peer on source cluster, accetp vserver peer on destination cluster.
4. check sync volume language and size is same. Check system default job schedule “5min" and use it.
5. any time difference between clusters must be less than 300 seconds. Same  time zones.
6. Create snapmirror relationship on destination cluster only. Initialize snapmirror relationship.
7. quiesce snapmirror; break snapmirror; resync snapmirror;
```
## create intercluster lif with cluster name vserver on each source cluster nodes
```
liyang::> net int create -vserver liyang -lif ic1 -home-node liyang-01 -home-port e0c -role intercluster -address 192.168.8.71 -netmask 255.255.255.0 
  (network interface create)

liyang::> net int create -vserver liyang -lif ic2 -home-node liyang-02 -home-port e0c -role intercluster -address 192.168.8.72 -netmask 255.255.255.0
  (network interface create)

liyang::> net int show -role intercluster 
  (network interface show)
            Logical    Status     Network            Current       Current Is
Vserver     Interface  Admin/Oper Address/Mask       Node          Port    Home
----------- ---------- ---------- ------------------ ------------- ------- ----
liyang
            ic1          up/up    192.168.8.71/24    liyang-01     e0c     true
            ic2          up/up    192.168.8.72/24    liyang-02     e0c     true
2 entries were displayed.
```
## create intercluster lif with cluster name vserver on each destination cluster nodes.
```
samli::> net int create -vserver samli -lif ic1 -home-node samli-01 -home-port e0c -role intercluster -address 192.168.8.73 -netmask 255.255.255.0
  (network interface create)

samli::> net int create -vserver samli -lif ic2 -home-node samli-02 -home-port e0c -role intercluster -address 192.168.8.74 -netmask 255.255.255.0
  (network interface create)

samli::> net int show -role data
  (network interface show)
            Logical    Status     Network            Current       Current Is
Vserver     Interface  Admin/Oper Address/Mask       Node          Port    Home
----------- ---------- ---------- ------------------ ------------- ------- ----
cifs1
            cifs1        up/up    192.168.8.47/24    samli-01      e0c     true

samli::> net int show -role intercluster 
  (network interface show)
            Logical    Status     Network            Current       Current Is
Vserver     Interface  Admin/Oper Address/Mask       Node          Port    Home
----------- ---------- ---------- ------------------ ------------- ------- ----
samli
            ic1          up/up    192.168.8.73/24    samli-01      e0c     true
            ic2          up/up    192.168.8.74/24    samli-02      e0c     true
2 entries were displayed.
```
## Create cluster peer between source cluster and destination cluster. Create cluster peer on source cluster first.
```
liyang::> cluster peer show
This table is currently empty.

liyang::> cluster peer create -address-family ipv4 -peer-addrs 192.168.8.73,192.168.8.74

Notice: Use a generated passphrase or choose a passphrase of 8 or more characters. To ensure the
        authenticity of the peering relationship, use a phrase or sequence of characters that would be
        hard to guess.

Enter the passphrase: 
Confirm the passphrase: 

Notice: Now use the same passphrase in the "cluster peer create" command in the other cluster.

liyang::> cluster peer show                                                             
Peer Cluster Name         Cluster Serial Number Availability   Authentication
------------------------- --------------------- -------------- --------------
samli                     -                     Unavailable    pending
```
## Create cluster peer on destination cluster 
```
samli::> cluster peer show
This table is currently empty.

samli::> cluster peer create -address-family ipv4 -peer-addrs 192.168.8.71,192.168.8.72

Notice: Use a generated passphrase or choose a passphrase of 8 or more characters. To ensure the
        authenticity of the peering relationship, use a phrase or sequence of characters that would be
        hard to guess.

Enter the passphrase: 
Confirm the passphrase: 

samli::> cluster peer show
Peer Cluster Name         Cluster Serial Number Availability   Authentication
------------------------- --------------------- -------------- --------------
liyang                    1-80-000008           Available      ok
```
## create vserver peer between source cluster vserver and destination cluster vserver. create vserver peer on source cluster first.
```
liyang::> vserver peer show
There are no Vserver peer relationships.
  
liyang::> vserver peer create -vserver cifsli -peer-vserver cifs1 -applications snapmirror -peer-cluster samli             

Info: [Job 89] 'vserver peer create' job queued 

liyang::> vserver peer show
            Peer        Peer                           Peering        Remote
Vserver     Vserver     State        Peer Cluster      Applications   Vserver
----------- ----------- ------------ ----------------- -------------- ---------
cifsli      cifs1       initiated    samli             snapmirror     cifs1
```
## check source volume language
```
liyang::> vol show -vserver cifsli -fields language 
vserver volume language 
------- ------ -------- 
cifsli  root1  C.UTF-8  
cifsli  vol1   C.UTF-8  
cifsli  vol2   C.UTF-8  
3 entries were displayed.
```
## accept vserver peer on destination cluster vserver
```
samli::> vserver peer accept -vserver cifs1 -peer-vserver cifsli             

Info: [Job 36] 'vserver peer accept' job queued 

samli::> vserver peer show
            Peer        Peer                           Peering        Remote
Vserver     Vserver     State        Peer Cluster      Applications   Vserver
----------- ----------- ------------ ----------------- -------------- ---------
cifs1       cifsli      peered       liyang            snapmirror     cifsli
```
## check destination volume language
```
samli::> vol show -vserver cifs1 -volume vol1 -fields language 
vserver volume language 
------- ------ -------- 
cifs1   vol1   C.UTF-8
```
## create volume on destination cluster vserver for sync source cluster vserver volume. need volume type DP
```
samli::> vol create -vserver cifs1 -volume vol1 -size 10g -aggregate aggr1_01 -type dp                  
[Job 38] Job succeeded: Successful                                                                     
```
## create snapmirror relationship on destination cluster only 
```
samli::> snapmirror create -source-path cifsli:vol1 -destination-path cifs1:vol1 -type dp -policy DPDefault -schedule 5min                     
```
## view ontap system default job schedule 
```
samli::> job schedule show
Cluster       Name         Type     Description
------------- ----------- --------- ————————————-------------------
samli
              5min        cron      @:00,:05,:10,:15,:20,:25,:30,:35,:40,:45,:50,:55
              8hour       cron      @2:15,10:15,18:15

samli::> snapmirror show                                                                                
                                                                       Progress
Source            Destination Mirror  Relationship   Total             Last
Path        Type  Path        State   Status         Progress  Healthy Updated
----------- ---- ------------ ------- -------------- --------- ------- --------
cifsli:vol1 XDP  cifs1:vol1   Uninitialized 
                                      Idle           -         true    -
```
## initialize snapmirror relationship
```
samli::> snapmirror initialize -destination-path cifs1:vol1  
Operation is queued: snapmirror initialize of destination "cifs1:vol1".                                

samli::> snapmirror show
                                                                       Progress
Source            Destination Mirror  Relationship   Total             Last
Path        Type  Path        State   Status         Progress  Healthy Updated
----------- ---- ------------ ------- -------------- --------- ------- --------
cifsli:vol1 XDP  cifs1:vol1   Uninitialized 
                                      Transferring   18.95KB   true    03/28 20:59:54


samli::> snapmirror show
                                                                       Progress
Source            Destination Mirror  Relationship   Total             Last
Path        Type  Path        State   Status         Progress  Healthy Updated
----------- ---- ------------ ------- -------------- --------- ------- --------
cifsli:vol1 XDP  cifs1:vol1   Snapmirrored 
                                      Idle           -         true    -

samli::> cluster peer show
Peer Cluster Name         Cluster Serial Number Availability   Authentication
------------------------- --------------------- -------------- --------------
liyang                    1-80-000008           Available      ok

samli::> vserver peer show
            Peer        Peer                           Peering        Remote
Vserver     Vserver     State        Peer Cluster      Applications   Vserver
----------- ----------- ------------ ----------------- -------------- ---------
cifs1       cifsli      peered       liyang            snapmirror     cifsli
```
## menual trigger snapmirror sync
```
samli::> snapmirror update -destination-path cifs1:vol1
```
## stop snapmirror relationship
```
samli::> snapmirror quiesce -destination-path cifs1:vol1

samli::> snapmirror show
                                                                       Progress
Source            Destination Mirror  Relationship   Total             Last
Path        Type  Path        State   Status         Progress  Healthy Updated
----------- ---- ------------ ------- -------------- --------- ------- --------
cifsli:vol1 XDP  cifs1:vol1   Snapmirrored 
                                      Quiesced       -         true    -
```
## break snapmirror relationship
```
samli::> snapmirror break -destination-path cifs1:vol1
Operation succeeded: snapmirror break for destination "cifs1:vol1".                                    

samli::> snapmirror show                              
                                                                       Progress
Source            Destination Mirror  Relationship   Total             Last
Path        Type  Path        State   Status         Progress  Healthy Updated
----------- ---- ------------ ------- -------------- --------- ------- --------
cifsli:vol1 XDP  cifs1:vol1   Broken-off 
                                      Idle           -         true    -
```
## resync snapmirror relationship
```
samli::> snapmirror resync -destination-path cifs1:vol1

Warning: All data newer than Snapshot copy
         snapmirror.e9b732f3-5153-11e9-9922-000c290a004c_2149097379.2019-03-28_211500 on volume
         cifs1:vol1 will be deleted.
Do you want to continue? {y|n}: y
Operation is queued: initiate snapmirror resync to destination "cifs1:vol1".                           

samli::> snapmirror show
                                                                       Progress
Source            Destination Mirror  Relationship   Total             Last
Path        Type  Path        State   Status         Progress  Healthy Updated
----------- ---- ------------ ------- -------------- --------- ------- --------
cifsli:vol1 XDP  cifs1:vol1   Snapmirrored 
                                     Idle           -         true    -
```
## view snapmirror attribute info
```
samli::> snapmirror show -destination-path vol1 -instance 

                            Source Path: cifsli:vol1
                       Destination Path: cifs1:vol1
                      Relationship Type: XDP
                Relationship Group Type: none
                    SnapMirror Schedule: 5min
                 SnapMirror Policy Type: async-mirror
                      SnapMirror Policy: DPDefault
                            Tries Limit: -
                      Throttle (KB/sec): unlimited
                           Mirror State: Snapmirrored
```