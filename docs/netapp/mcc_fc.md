## Configure the FC switches
```
fosconfig --disable vf
switchcfgpersistentdisable
cfgDisable
cfgClear
cfgSave
configDefault
portcfgdefault 0
portcfgdefault 1
licenseport -show
userconfig --change root -e yes
rootaccess --set consoleonly
licenseport --method dynamic
fastBoot
switchShow
ipAddrShow
configDownload
switchcfgpersistentenable
portcfgspeed 20 LE 1
portcfgspeed 21 LE 1
portcfgspeed 0 16
switchshow
fabricshow
trunkshow
cfgshow 
zoneshow
```

## Configure the ATTO
```
set closePort none
saveConfiguration
set bridgename ATTO_A_01
set ipaddress mp1 172.16.50.13
set ipsubnetmask mp1  255.255.255.0
set ipgateway mp1 172.16.50.1
set ipdhcp mp1 disabled
set SNMP enabled
sasportdisable B
set fcdatarate 1 32Gb
```

## Configure storage
```
维护模式下
ha-config modify chassis mcc  or  mcc-2n  or  mccip
ha-config modify controller mcc  or  mcc-2n  or  mccip
ucadmin modify -t fcvi 0e
ucadmin modify -t fcvi 0f
ucadmin modify -t initiator 2a
ucadmin modify -t initiator 2b
ucadmin modify -t initiator 2c
ucadmin modify -t initiator 2d

assign磁盘
disk assign DB610S_B_1:9.126L27 -p 0  or  disk assign -shelf FC_switch_A_1:1-4.shelf3 -p 0
disk assign DB610S_A_1:8.126L53 -p 1  or  disk assign -shelf FC_switch_B_1:1-4.shelf4 -p 1

cluster setup
license add

创建intercluster
(9.6 and later)
network interface create -vserver system_sVM -lif LIF_name -service-policy default-intercluster -home-node node -home-port port -address port_ip -netmask -failover-group failover_group
(9.5 and earlier)
network interface create -vserver system_SVM -lif LIF_name -role intercluster -home-node node -home-port port -address port_IP -netmask netmask -failover-group failover_group
network interface show -service-policy default-intercluster

创建cluster peer，两个集群都需要操作
cluster peer create -peer-addrs 192.140.112.101,192.140.112.102

根聚合mirror
aggr mirror -aggregate aggr0_MCC7000F_B_01
aggr show-resync-status
aggr show

创建data aggr和mirror
aggr create -aggregate ssd_MCC7000F_B_01 -diskcount 38 -raidtype raid_dp -maxraidsize 19 -mirror true  -node MCC7000F-B-01

配置Metrocluster
每个集群上应至少有两个非根镜像数据聚合
metrocluster configure
若为单镜像数据聚合，需加参数，advanced模式下
MetroCluster configure -allow-with-one-aggregate true

添加FC交换机和ATTO到MCC
storage switch add -address ipaddress
storage bridge add -address 0.0.0.0 -managed-by in-band -name bridge-name
storage bridge add -address bridge-ip-address -name bridge-name

检查配置
metrocluster check run
metrocluster check show
MetroCluster check aggregate show
MetroCluster check cluster show
MetroCluster check config-replication show
MetroCluster check lif show
MetroCluster check node show
```