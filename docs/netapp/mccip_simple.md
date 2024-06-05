## halt ontap to loader
```
set-defaults
printenv partner-sysid
setenv partner-sysid 538003783
saveenv
printenv partner-sysid
```
## clean ADP and ownership select 9a on boot menu
```
boot_ontap menu
```
## select boot menu 5 to Maintenance mode
```
ha-config show
ha-config modify controller mccip
ha-config modify chassis mccip
ucadmin show
ucadmin modify -m cna e0c
```
## assign disk for ontap root aggr on Maintenance mode
```
disk assign 0a.10.15
disk assign 0a.10.16
disk assign 0a.10.17
```
## halt Maintenance mode to boot menu select 4 initialize
```
boot_ontap menu
```
## cluster setup
```
storage failover modify -mode ha -node * 
storage disk option modify -node * -autoassign off
disk removeowner -disk 1.0.* 
timezone -timezone Asia/Shanghai 
cluster show
node show
storage failover show
```
## create crs broadcast-domain for intercluster on both site cluster
```
broadcast-domain show
broadcast-domain remove-ports -broadcast-domain Default -ports MCCIP_A-01:e0c
broadcast-domain remove-ports -broadcast-domain Default -ports MCCIP_A-01:e0d
broadcast-domain remove-ports -broadcast-domain Default -ports MCCIP_A-01:e0e
broadcast-domain remove-ports -broadcast-domain Default -ports MCCIP_A-01:e0f

broadcast-domain create -broadcast-domain crs -mtu 1500
broadcast-domain add-ports -broadcast-domain crs -ports MCCIP_A-01:e0c
broadcast-domain add-ports -broadcast-domain crs -ports MCCIP_A-02:e0c 
```
## Create intercluster LIFs on MCCIP_A site
```
net int create -vserver MCCIP_A -lif MCCIP_A-01-ic1 -role intercluster -address 192.168.8.211 -netmask 255.255.255.0 -service-policy default-intercluster -home-node MCCIP_A-01 -status-admin up -home-port e0c
net int create -vserver MCCIP_A -lif MCCIP_A-02-ic1 -role intercluster -address 192.168.8.212 -netmask 255.255.255.0 -service-policy default-intercluster -home-node MCCIP_A-02 -status-admin up -home-port e0c
```
## Create intercluster LIFs on MCCIP_B site
```
net int create -vserver MCCIP_B -lif MCCIP_B-01-ic1 -role intercluster -address 192.168.8.213 -netmask 255.255.255.0 -service-policy default-intercluster -home-node MCCIP_B-01 -status-admin up -home-port e0c
net int create -vserver MCCIP_B -lif MCCIP_B-02-ic1 -role intercluster -address 192.168.8.214 -netmask 255.255.255.0 -service-policy default-intercluster -home-node MCCIP_B-02 -status-admin up -home-port e0c

net int show -role intercluster
```
## create cluster peer on MCCIP_A site
```
cluster peer create -address-family ipv4 -peer-addrs 192.168.8.213,192.168.8.214
cluster peer show
```
## create cluster peer on MCCIP_B site
```
cluster peer create -address-family ipv4 -peer-addrs 192.168.8.211,192.168.8.212
cluster peer show
```

## Create metrocluster DR-group on only one MCCIP_A site
```
metrocluster configuration-settings dr-group create -partner-cluster MCCIP_B -local-node MCCIP_A-01 -remote-node MCCIP_B-01
metrocluster configuration-settings show-status
```
## Configuring  MetroCluster interfaces only one MCCIP_A site
```
metrocluster configuration-settings interface create -cluster-name MCCIP_A -home-node MCCIP_A-01 -home-port e0a-10 -address 10.1.1.1 -netmask 255.255.255.0
metrocluster configuration-settings interface create -cluster-name MCCIP_A -home-node MCCIP_A-02 -home-port e0a-10 -address 10.1.1.2 -netmask 255.255.255.0
metrocluster configuration-settings interface create -cluster-name MCCIP_A -home-node MCCIP_A-01 -home-port e0b-20 -address 10.1.2.1 -netmask 255.255.255.0
metrocluster configuration-settings interface create -cluster-name MCCIP_A -home-node MCCIP_A-02 -home-port e0b-20 -address 10.1.2.2 -netmask 255.255.255.0

metrocluster configuration-settings interface create -cluster-name MCCIP_B -home-node MCCIP_B-01 -home-port e0a-10 -address 10.1.1.3 -netmask 255.255.255.0
metrocluster configuration-settings interface create -cluster-name MCCIP_B -home-node MCCIP_B-02 -home-port e0a-10 -address 10.1.1.4 -netmask 255.255.255.0
metrocluster configuration-settings interface create -cluster-name MCCIP_B -home-node MCCIP_B-01 -home-port e0b-20 -address 10.1.2.3 -netmask 255.255.255.0
metrocluster configuration-settings interface create -cluster-name MCCIP_B -home-node MCCIP_B-02 -home-port e0b-20 -address 10.1.2.4 -netmask 255.255.255.0

metrocluster configuration-settings interface show
```
## create MetroCluster connect
```
metrocluster configuration-settings connection connect
```
## check iscsi-initiator
```
set adv
storage iscsi-initiator show
metrocluster node show
```
## disk assign owner on pool0 or pool1
```
disk show -container-type unassigned
disk show -type ssd -container-type unassigned
disk show -type sas -container-type unassigned

disk assign -owner MCCIP_A-01 -disklist 1.0.*
disk assign -owner MCCIP_A-01 -pool 1 -disklist MCCIP_A-01:0m.i1.4L9

disk assign -owner MCCIP_A-01 -type SSD -pool 0 -count 12
disk assign -owner MCCIP_A-01 -type SSD -pool 1 -count 12

disk assign -owner MCCIP_A-01 -pool 0 -count 12
disk assign -owner MCCIP_A-01 -type sas -pool 1 -count 3

disk show -type sas -owner MCCIP_A-01 -pool Pool0
disk show -type sas -owner MCCIP_A-01 -pool Pool1
disk show -type sas -owner MCCIP_A-01 -pool Pool0 -container-type spare
disk removeowner 1.0.2
```
## mirror root aggr 
```
aggr show
aggr mirror -aggregate aggr0_MCCIP_A_01
aggr mirror -aggregate aggr0_MCCIP_A_02
aggr mirror -aggregate aggr0_MCCIP_B_01
aggr mirror -aggregate aggr0_MCCIP_B_02
```
## create data aggr for mirror
```
aggr create -aggregate MCCIP_A_01_ssd_aggr -diskcount 22 -mirror true -disktype SSD
aggr create -aggregate MCCIP_A_01_ssd_aggr -diskcount 22 -mirror true -disktype SAS
```
## allow only one data aggr on both site
```
set adv
metrocluster configure -allow-with-one-aggregate true
```
## metrocluster configure
```
metrocluster configure
metrocluster show
```
## add mediator for metrocluster
```
metrocluster configuration-settings mediator add -mediator-address 192.168.8.14
Adding the mediator and enabling Automatic Unplanned Switchover. It may take a few minutes to complete.   
Please enter the username for the mediator: mediatoradmin
Please enter the password for the mediator: 
Confirm the mediator password:

metrocluster configuration-settings mediator remove
```
## metrocluster check
```
metrocluster show
metrocluster check run
metrocluster check show
metrocluster check aggregate show
metrocluster check cluster show
metrocluster check config-replication show
metrocluster check lif show
metrocluster check node show

MCCIP_A::> metrocluster show

Configuration: IP-fabric

Cluster                        Entry Name             State
------------------------------ ---------------------- ---------------------
Local: MCCIP_A
                                 Configuration State  configured
                                                Mode  normal
                                 AUSO Failure Domain  auso-on-cluster-disaster
Remote: MCCIP_B
                                 Configuration State  configured
                                                Mode  normal
                                 AUSO Failure Domain  auso-on-cluster-disaster
````
## metrocluster failover testing
```
metrocluster switchover
metrocluster show
metrocluster switchback

#If the system is running ONTAP 9.4 or earlier, heal the data aggregate
metrocluster heal aggregates
metrocluster heal root-aggregates
```
## performance and latency monitor
```
qos statistics performance show
qos statistics latency show
```