## create svm
```
vserver create -vserver fcp_vs1 -rootvolume fcp_vs1_root -aggregate MCCIP_A_02_sas_aggr
vserver fcp create -vserver fcp_vs1
MCCIP_A::> vs fcp show
  (vserver fcp show)
                                        Status
Vserver    Target Name                  Admin
---------- ---------------------------- ------
fcp_vs1    20:01:d0:39:ea:2f:fd:6b      up
```
## create fcp least two lifs on each node 
```
network interface create -vserver fcp_vs1 -lif fcp_lif1  -role data -data-protocol fcp -home-node node-01 -home-port 0e -status-admin up 
network interface create -vserver fcp_vs1 -lif fcp_lif2  -role data -data-protocol fcp -home-node node-01 -home-port 0f -status-admin up 
network interface create -vserver fcp_vs1 -lif fcp_lif1  -role data -data-protocol fcp -home-node node-02 -home-port 0e -status-admin up 
network interface create -vserver fcp_vs1 -lif fcp_lif2  -role data -data-protocol fcp -home-node node-02 -home-port 0f -status-admin up 
```
## create volume and lun
```
vol create -vserver fcp_vs1 -volume vol01 -size 10t -aggregate MCCIP_A_01_sas_aggr
lun create -vserver fcp_vs1 -volume vol01 -lun lun01 -size 9t -ostype vmware
```
## create igroup with one server to one igroup
```
lun igroup create -vserver fcp_vs1 -igroup esxi01 -protocol fcp -ostype vmware
lun igroup add -vserver fcp_vs1 -igroup esxi01 -initiator 10:00:00:10:9b:c0:08:1c
```
## create lun map
```
lun mapping create -vserver fcp_vs1 -path /vol/vol01/lun01 -igroup esxi01
```
## check review
```
igroup show
vol show
lun show
lun mapping show

MCCIP_A::> lun show 
Vserver   Path                            State   Mapped   Type        Size
--------- ------------------------------- ------- -------- -------- --------
fcp_vs1   /vol/APP_mirror_vol/APP_mirror_lun 
                                          online  mapped   vmware     2.90TB
fcp_vs1   /vol/DB_mirror_vol/DB_mirror_lun 
                                          online  mapped   vmware     2.90TB
fcp_vs1   /vol/DB_vol/DB_lun              online  mapped   vmware    14.50TB
3 entries were displayed.

MCCIP_A::> lun mapping show
Vserver    Path                                      Igroup   LUN ID  Protocol
---------- ----------------------------------------  -------  ------  --------
fcp_vs1    /vol/APP_mirror_vol/APP_mirror_lun        esxi01        0  fcp
fcp_vs1    /vol/APP_mirror_vol/APP_mirror_lun        esxi02        2  fcp
fcp_vs1    /vol/DB_mirror_vol/DB_mirror_lun          esxi01        1  fcp
fcp_vs1    /vol/DB_mirror_vol/DB_mirror_lun          esxi02        1  fcp
fcp_vs1    /vol/DB_vol/DB_lun                        esxi01        2  fcp
fcp_vs1    /vol/DB_vol/DB_lun                        esxi02        0  fcp
6 entries were displayed.

MCCIP_A::> igroup show 
Vserver   Igroup       Protocol OS Type  Initiators
--------- ------------ -------- -------- ------------------------------------
fcp_vs1   esxi01       fcp      vmware   10:00:00:10:9b:c0:08:1c
                                         10:00:00:10:9b:c0:08:20
fcp_vs1   esxi02       fcp      vmware   10:00:00:10:9b:c0:08:42
                                         10:00:00:10:9b:c0:08:8a
fcp_vs2-mc 
          esxi01       fcp      vmware   10:00:00:10:9b:c0:08:1c
                                         10:00:00:10:9b:c0:08:20
fcp_vs2-mc 
          esxi02       fcp      vmware   10:00:00:10:9b:c0:08:42
                                         10:00:00:10:9b:c0:08:8a
4 entries were displayed.
```
## fc zone on fc-switch DB610S
```
SAN01:admin> alicreate esxi01_s1t1,"10:00:00:10:9b:c0:08:1c"
SAN01:admin> alicreate esxi02_s1t1,"10:00:00:10:9b:c0:08:42"

SAN01:admin> alicreate DM5000H,"20:07:d0:39:ea:2f:fd:6b;20:09:d0:39:ea:2f:fd:6b;20:02:d0:39:ea:2f:ec:a1;20:04:d0:39:ea:2f:ec:a1;20:08:d0:39:ea:2f:fd:6b;20:0a:d0:39:ea:2f:fd:6b;20:01:d0:39:ea:2f:ec:a1;20:03:d0:39:ea:2f:ec:a1"
SAN01:admin> alishow

SAN01:admin> zonecreate esxi01_DM5000H,"esxi01_s1t1;DM5000H"
SAN01:admin> zonecreate esxi02_DM5000H,"esxi02_s1t1;DM5000H"
SAN01:admin> zoneshow

SAN01:admin> cfgcreate cfg1,"esxi01_DM5000H;esxi02_DM5000H"
SAN01:admin> cfgsave
SAN01:admin> cfgenable cfg1
SAN01:admin> cfgshow

SAN01:admin> cfgshow
Defined configuration:
 cfg:   cfg1    esxi01_DM5000H; esxi02_DM5000H
 zone:  esxi01_DM5000H
                esxi01_s1t1; DM5000H
 zone:  esxi02_DM5000H
                esxi02_s1t1; DM5000H
 alias: DM5000H 20:07:d0:39:ea:2f:fd:6b; 20:09:d0:39:ea:2f:fd:6b; 
                20:02:d0:39:ea:2f:ec:a1; 20:04:d0:39:ea:2f:ec:a1; 
                20:08:d0:39:ea:2f:fd:6b; 20:0a:d0:39:ea:2f:fd:6b; 
                20:01:d0:39:ea:2f:ec:a1; 20:03:d0:39:ea:2f:ec:a1
 alias: esxi01_s1t1
                10:00:00:10:9b:c0:08:1c
 alias: esxi02_s1t1
                10:00:00:10:9b:c0:08:42

Effective configuration:
 cfg:   cfg1
 zone:  esxi01_DM5000H
                10:00:00:10:9b:c0:08:1c
                20:07:d0:39:ea:2f:fd:6b
                20:09:d0:39:ea:2f:fd:6b
                20:02:d0:39:ea:2f:ec:a1
                20:04:d0:39:ea:2f:ec:a1
                20:08:d0:39:ea:2f:fd:6b
                20:0a:d0:39:ea:2f:fd:6b
                20:01:d0:39:ea:2f:ec:a1
                20:03:d0:39:ea:2f:ec:a1
 zone:  esxi02_DM5000H
                10:00:00:10:9b:c0:08:42
                20:07:d0:39:ea:2f:fd:6b
                20:09:d0:39:ea:2f:fd:6b
                20:02:d0:39:ea:2f:ec:a1
                20:04:d0:39:ea:2f:ec:a1
                20:08:d0:39:ea:2f:fd:6b
                20:0a:d0:39:ea:2f:fd:6b
                20:01:d0:39:ea:2f:ec:a1
                20:03:d0:39:ea:2f:ec:a1
```