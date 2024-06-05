##  Broadcom clear configuration defautl user:admin not password
```
enable
erase startup-config
reload
```
## setup management port
```
serviceport protocol none
serviceport ip 192.168.8.100 255.255.255.0 192.168.8.1
show serviceport
configure
crypto key generate rsa
crypto key generate dsa
username admin password netapp123 level 15
ip ssh server enable
hostname ip_switch_A_1
write memory
```
## update firmware
```
copy http://192.168.8.200/efos-3.4.3.1.stk backup
boot system backup
show bootvar
write memory
reload
```
## import RCF to switch
```
java -jar RcfFileGenerator.jar
copy http://192.168.8.200/BES-53248_v1.31_Switch-A1.txt nvram:script BES-53248_v1.31_Switch-A1.scr
script list
script apply BES-53248_v1.31_Switch-A1.scr
write memory
reload
```
## Port Usage:
```
NetApp Reference Configuration File (RCF)

Switch    : BES-53248
Filename  : BES-53248_v1.31_Switch-A1.txt
Platforms : FAS2750, AFF-A220, FAS8200, AFF-A300
Date      : Generator version: v1.1a, file creation time: 2021-03-30, 17:05:35

Port Usage:
Ports 01 - 04: Intra-Cluster Ports
Ports 05 - 08: MetroCluster-IP Ports, VLAN 10
Ports 09 - 12: Shared Intro-Cluster and MetroCluster-IP Ports, VLAN 1,10
Ports 13 - 16: MetroCluster-IP ISL Ports, VLAN 10, LAG 1/10, 10G / 25G
Ports 17 - 48: Unused Ports, 10G / 25G
Ports 49 - 54: Unused Ports, 100G
Ports 55 - 56: Intra-Cluster ISL Ports, LAG 1/1, 100G
```