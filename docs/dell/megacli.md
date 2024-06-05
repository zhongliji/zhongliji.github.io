## Megacli
```
megacli -LDInfo -Lall -aALL 	查raid级别
megacli -AdpAllInfo -aALL 	查raid卡信息
megacli -PDList -aALL 		查看硬盘信息
megacli -AdpBbuCmd -aAll 	查看电池信息
megacli -FwTermLog -Dsply -aALL 查看raid卡日志
megacli -LDInfo -LALL -aAll	查看LD逻辑磁盘
megacli -ldpdinfo -aall		查看LDPD逻辑与物理磁盘
megacli -cfgdsply -aALL 	显示Raid卡型号，Raid设置，Disk相关信息

megacli -PDList -a0

megacli -CfgForeign -Dsply -a0

megacli -CfgForeign -Clear -a0

megacli -CfgForeign -Import -a0

megacli -LDInfo -LAll -a0

megacli -CfgLdadd -r5 [32:3,32:4,32:5] WB Direct -sz100gb -strpsz64 -a0	 创建raid5

megacli -CfgLdadd -r5 [32:3,32:4,32:5] WB Direct -Hsp[32:6] -a0	创建raid5及热备盘

megacli -ldbi -showprog -lall -a0  查看后台初始化过程

megacli -CfgLdDel -L1 -a0				删除LD	

megacli -LDRecon -Start -r5 -Add -PhysDrv[32:3] -L0 -a0		在线添加物理磁盘

megacli -PDOffline -PhysDrv [32:4] -a0  强制下线硬盘

megacli -PDOnline -PhysDrv [32:4] -a0 强制上线硬盘

megacli -PDHSP -Set [-EnclAffinity] [-nonRevertible] -PhysDrv[32:6] -a0 创建全局热备盘

megacli -pdhsp --Rmv -physdrv[32:5] -a0 删除热备盘

megacli -PDRbld -ShowProg -PhysDrv [32:5] -a0 查看物理磁盘重建进度

megacli -pdmakejbod -physdrv[x:x] -a0   #disk make non-raid mode

megacli -pdlist -a0 |grep -i -e "enclosure device id" -e "slot number" -e "raw size" -e "firmware state"

megacli -pdmakegood -physdrv[32:7] -force -a0

megacli -h |grep -i pdmake

megacli -h |grep -i cfg

megacli -cfgallfreedrv -r1 -a0

megacli -cfglddel -l1 -a0

megacli -cfgclr -force -a0

megacli -adpbootdrive -set -l0 -a0
```