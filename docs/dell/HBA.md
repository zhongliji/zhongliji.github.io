## LINUX下HBA卡配置
```
首先确认是哪种光纤卡：
lspci | grep -i fibre

光纤卡基本上就以下两种：
Emulex：   lsmod |grep lpfc
qlogic：   lsmod |grep qla

如果正确安装了驱动：

cat /sys/class/fc_host/hostx/port_name  #查看WWN

[root@localhost ~]# cd /sys/class/fc_host/
[root@localhost fc_host]# ls
host7  host8
[root@localhost ~]# cat /sys/class/fc_host/host7/port_name
0x10000090fa07e3ac
[root@localhost ~]# cat /sys/class/fc_host/host8/port_name
0x10000090fa07e3ad

# update hba status
echo "1" > /sys/class/fc_host/host7/issue_lip
```