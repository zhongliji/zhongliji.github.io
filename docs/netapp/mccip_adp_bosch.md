## BES-53248配置,4个交换机配置步骤一样
#  使用RcfFileGenerator软件生成4个rcf文件
```
1. 配置管理口
(Routing) >enable
(Routing) #serviceport protocol none
(Routing) #serviceport ip 10.200.255.16 255.255.255.0 10.200.255.1
(Routing) #show serviceport

2. 配置SSH服务
(Routing) #configure
(Routing) (Config)#crypto key generate rsa
(Routing) (Config)#crypto key generate dsa
(Routing) (Config)#crypto key generate ecdsa 256
(Routing) (Config)#end
(Routing) #ip ssh server enable

3. 修改交换机名字
(Routing) # hostname cgqbnas01mp-01-ic1
(cgqbnas01mp-01-ic1) #write memory

4. 升级交换机固件
(cgqbnas01mp-01-ic1) #copy http://10.200.255.100/switchsoftware/EFOS-3.10.0.3.stk backup
(cgqbnas01mp-01-ic1) #boot system backup
(cgqbnas01mp-01-ic1) #show bootvar
(cgqbnas01mp-01-ic1) #write memory
(cgqbnas01mp-01-ic1) #reload

5. 导入rcf文件
(cgqbnas01mp-01-ic1) #copy http://10.200.255.16/RcfFiles/BES-53248_v2.11_Switch-A1.txt nvram:script BES-53248_v2.11_Switch-A1.scr
(cgqbnas01mp-01-ic1) #script list
(cgqbnas01mp-01-ic1) #script apply BES-53248_v2.11_Switch-A1.scr
(cgqbnas01mp-01-ic1) #write memory
(cgqbnas01mp-01-ic1) #reload
```
## 存储配置(ADP)-A250，控制器自带24块NVME-SSD
## 客户要求6块盘不分区，不做mirror，创建一个aggr给到node2，其余18块盘分区，将root-aggr使用的4块盘的P1和P2分区给到node1，并创建一个mirrored aggr给到node1
```
1. loader修改env参数，使root aggr只使用4块盘的P3分区,且在存储初始化完成后只有12块盘分区
LOADER-A> set-dafault
LOADER-A> setenv root-configuration "-d 2 -p 2 -s 0"
LOADER-A> saveenv 
LOADER-A> printenv

2. 9a清除掉所有磁盘的分区和owner

3. 维护模式下修改controller和chassis模式为mcc ip
*> ha-config modify controller mccip
*> ha-config modify chassis mccip

3. 维护模式下分别分配4块磁盘到每个node
*> disk assign 0n.1
*> disk assign 0n.2
*> disk assign 0n.3
*> disk assign 0n.4

4. boot menu按7重装系统

5. boot menu按4初始化

6. 设置集群配置

7. 修改broadcast-domain

8. 安装客户要求，将root-aggr的4块盘的P1和P2给到node1
cgqbnas01mp::> set d
cgqbnas01mp::> disk option modify -node * -autoassign off
cgqbnas01mp::*> disk removeowner -disk 1.0.0 -data2 true
cgqbnas01mp-01> disk assign  0n.0P2 -s 538055272 -p 0

9. 将未分区的6块盘给到node2
cgqbnas01mp-02> disk assign 0n.5

10. 每个节点创建2个intercluster lif用于cluster peer
cgqbnas01mp::*> net int create -vserver cgqbnas01mp -lif cgqbnas01mp-01-ic1 -service-policy default-intercluster -home-node cgqbnas01mp-01 -home-port e0a -address 10.200.253.18 -netmask 255.255.255.0 -status-admin up -failover-group bcn

11. 创建cluster peer
cgqbnas01mp::*> cluster peer create -peer-addrs 10.200.253.28,10.200.253.29,10.200.253.30,10.200.253.31

12. 创建dr-group
cgqbnas01mp::*> metrocluster configuration-settings dr-group create -partner-cluster cgqbnas02mp -local-node cgqbnas01mp-01 -remote-node cgqbnas02mp-01

13. 配置和连接mcc ip接口用于复制每个节点的存储和非易失性缓存
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas01mp -home-node cgqbnas01mp-01 -home-port  e0c -address 10.10.0.1 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas01mp -home-node cgqbnas01mp-01 -home-port e0d -address 10.20.0.1 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas01mp -home-node cgqbnas01mp-02 -home-port e0c -address 10.10.0.11 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas01mp -home-node cgqbnas01mp-02 -home-port e0d -address 10.20.0.11 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas02mp -home-node cgqbnas02mp-01 -home-port e0c -address 10.10.0.2 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas02mp -home-node cgqbnas02mp-01 -home-port e0d -address 10.20.0.2 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas02mp -home-node cgqbnas02mp-02 -home-port e0c -address 10.10.0.12 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings interface create -cluster-name cgqbnas02mp -home-node cgqbnas02mp-02 -home-port e0d -address 10.20.0.12 -netmask 255.255.255.0
cgqbnas01mp::*> metrocluster configuration-settings connection connect

14. 连接mcc ip接口后，本地存储就能看到远端存储的磁盘了

15. 分配远端磁盘为本地磁盘的pool 1
cgqbnas01mp-02> disk assign 0n.5 
cgqbnas01mp-02> disk assign 0n.10
cgqbnas01mp-02> disk assign 0n.11
cgqbnas01mp-02> disk assign 0n.15
cgqbnas01mp-02> disk assign 0n.19
cgqbnas01mp-02> disk assign 0n.22

16. 对pool 1的磁盘进行分区，并重新分配owner，两个站点都要做
cgqbnas01mp::*> disk create-partition -source-disk 1.0.1 -target-disk cgqbnas01mp-01:0v.i1.0L9
cgqbnas01mp-01> disk show -n                                                                            
  DISK       OWNER                    POOL   SERIAL NUMBER         HOME                    DR HOME      
------------ -------------            -----  -------------         -------------           -------------
0v.i2.2L2P1  Not Owned                  NONE   22261Y800082NP001                                        
0v.i2.2L2P2  Not Owned                  NONE   22261Y800082NP002                                        
0v.i2.2L2P3  Not Owned                  NONE   22261Y800082NP003
cgqbnas01mp-01> disk assign 0v.i2.2L2P1 -p 1
cgqbnas01mp-01> disk assign 0v.i2.2L2P2 -p 1
cgqbnas01mp-01> disk assign 0v.i2.2L2P3 -p 1

17. 对root-aggr做mirror，两个站点都要做
cgqbnas01mp::*> storage aggregate mirror -aggregate aggr_root_01mp_01
cgqbnas01mp::*> storage aggregate mirror -aggregate aggr_root_01mp_02

18. 创建data aggr并mirror，两个站点都要做
cgqbnas01mp::*> aggr create -aggregate aggr1_ssd_01mp_01 -node cgqbnas01mp-01 -diskcount 14 -maxraidsize 14 -mirror
The layout for aggregate "aggr1_ssd_01mp_01" on node "cgqbnas01mp-01" would be:  
                                                                                 
First Plex                                                                       
                                                                                 
  RAID Group rg0, 7 disks (block checksum, raid_dp)                              
                                                      Usable Physical            
    Position   Disk                      Type           Size     Size            
    ---------- ------------------------- ---------- -------- --------            
    shared     1.0.1                     SSD-NVM           -        -            
    shared     1.0.2                     SSD-NVM           -        -            
    shared     1.0.3                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.4                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.6                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.7                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.8                     SSD-NVM     847.3GB  847.4GB            
                                                                                 
  RAID Group rg1, 7 disks (block checksum, raid_dp)                              
                                                      Usable Physical            
    Position   Disk                      Type           Size     Size            
    ---------- ------------------------- ---------- -------- --------            
    shared     1.0.1                     SSD-NVM           -        -            
    shared     1.0.2                     SSD-NVM           -        -            
    shared     1.0.3                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.4                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.6                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.7                     SSD-NVM     847.3GB  847.4GB            
    shared     1.0.8                     SSD-NVM     847.3GB  847.4GB            
                                                                                 
Second Plex                                                                      
                                                                                 
  RAID Group rg0, 7 disks (block checksum, raid_dp)                              
                                                      Usable Physical            
    Position   Disk                      Type           Size     Size            
    ---------- ------------------------- ---------- -------- --------            
    shared     cgqbnas01mp-01:0v.i1.0L18 SSD-NVM           -        -            
    shared     cgqbnas01mp-01:0m.i2.3L8  SSD-NVM           -        -            
    shared     cgqbnas01mp-01:0m.i1.1L15 SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0m.i1.0L14 SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0v.i2.2L12 SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0m.i2.2L7  SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0v.i2.3L4  SSD-NVM     847.3GB  847.4GB            
                                                                                 
  RAID Group rg1, 7 disks (block checksum, raid_dp)                              
                                                      Usable Physical            
    Position   Disk                      Type           Size     Size            
    ---------- ------------------------- ---------- -------- --------            
    shared     cgqbnas01mp-01:0v.i1.0L18 SSD-NVM           -        -            
    shared     cgqbnas01mp-01:0m.i2.3L8  SSD-NVM           -        -            
    shared     cgqbnas01mp-01:0m.i1.1L15 SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0m.i1.0L14 SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0v.i2.2L12 SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0m.i2.2L7  SSD-NVM     847.3GB  847.4GB            
    shared     cgqbnas01mp-01:0v.i2.3L4  SSD-NVM     847.3GB  847.4GB            
                                                                                 
Aggregate capacity available for volume use would be 7.07TB.                     

19. 配置metrocluster
cgqbnas01mp::*> metrocluster configure -node-name cgqbnas01mp-01 -allow-with-one-aggregate true

20. 检查
cgqbnas01mp::*> metrocluster show                                    
                                                                     
Configuration: IP-fabric                                             
                                                                     
Cluster                        Entry Name             State          
------------------------------ ---------------------- ---------------
Local: cgqbnas01mp                                                   
                                 Configuration State  configured     
                                                Mode  normal         
                                 AUSO Failure Domain  auso-disabled  
Remote: cgqbnas02mp                                                  
                                 Configuration State  configured     
                                                Mode  normal         
                                 AUSO Failure Domain  auso-disabled  

                                       
```
