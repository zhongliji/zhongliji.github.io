## linux multipath
#  安装
```
yum -y install iscsi-initiator-utils
yum -y install device-mapper-multipath
chkconfig --level 35 multipathd on
cp /usr/share/doc/device-mapper-multipath-0.4.9/multipath.conf /etc/  (拷贝配置文件到/etc/目录下)
service multipathd start  （service multipathd status; service multipathd reload ）
lsmod | grep dm          检查多路径模块是否加载
```
#  查看iscsi initiator安装情况
```
rpm -qa | grep iscsi
```
#  建立multipath多路径文件
```
iscsiadm -m iface -I eth0 -o new
iscsiadm -m iface -I eth1 -o new
```
#  login
```
iscsiadm -m discovery -t sendtargets -p IP
iscsiadm -m node -l
```
#  启动服务
```
systemctl start multipathd
```
#  配置multipath
```
vi /etc/multipath.conf

defaults {
        user_friendly_names yes
        path_grouping_policy    multibus
        path_checker            tur
        failback                immediate
        no_path_retry           fail
}

blacklist {
        devnode "sda"
}

multipaths {
        multipath {
                wwid                    364ed2aa51864120b621ab51c2b9bf4b1
                alias                   ps6100
                path_grouping_policy    multibus	  # 路径组策略: failover 为主备模式； multibus 为负载均衡模式
                failback                immediate
                rr_weight               priorities
                no_path_retry           5
        }
#       multipath {
#               wwid                    1DEC_____321816758474
#               alias                   red
#       }
}
```
```
multipath –v2
```
```
multipath -ll
```