# LVM for iscsi-target 
```
pvcreate /dev/vdb
vgcreate vg1 /dev/vdb
lvcreate -n lv1 -L 2g vg1
lvcreate -n lv2 -L 200g vg1
```
# zero lv
```
dd if=/dev/zero of=/dev/vg1/lv1 bs=1M count=100
dd if=/dev/zero of=/dev/vg1/lv2 bs=1M count=100
```
# iscsi target
```
yum -y install targetcli
systemctl enable target
systemctl start target

[root@c08]# targetcli

/> ls
o- / .......................................................... [...]
  o- backstores ............................................... [...]
  | o- block ................................... [Storage Objects: 2]
  | | o- data ........ [/dev/vg1/lv2 (200.0GiB) write-thru activated]
  | | | o- alua .................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........ [ALUA state: Active/optimized]
  | | o- zone .......... [/dev/vg1/lv1 (2.0GiB) write-thru activated]
  | |   o- alua .................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........ [ALUA state: Active/optimized]
  | o- fileio .................................. [Storage Objects: 0]
  | o- pscsi ................................... [Storage Objects: 0]
  | o- ramdisk ................................. [Storage Objects: 0]
  o- iscsi ............................................. [Targets: 1]
  | o- iqn.2020-04.com.storage:liyang ..................... [TPGs: 1]
  |   o- tpg1 ................................ [no-gen-acls, no-auth]
  |     o- acls ........................................... [ACLs: 2]
  |     | o- iqn.1994-05.com.redhat:c06 ............ [Mapped LUNs: 2]
  |     | | o- mapped_lun0 ................... [lun0 block/zone (rw)]
  |     | | o- mapped_lun1 ................... [lun1 block/data (rw)]
  |     | o- iqn.1994-05.com.redhat:c07 ............ [Mapped LUNs: 2]
  |     |   o- mapped_lun0 ................... [lun0 block/zone (rw)]
  |     |   o- mapped_lun1 ................... [lun1 block/data (rw)]
  |     o- luns ........................................... [LUNs: 2]
  |     | o- lun0 .... [block/zone (/dev/vg1/lv1) (default_tg_pt_gp)]
  |     | o- lun1 .... [block/data (/dev/vg1/lv2) (default_tg_pt_gp)]
  |     o- portals ..................................... [Portals: 1]
  |       o- 192.168.20.148:3260 ............................... [OK]
  o- loopback .......................................... [Targets: 0]
```
# pcs host
```
yum -y install iscsi-initiator-utils
cat /etc/iscsi/initiatorname.iscsi 
InitiatorName=iqn.1994-05.com.redhat:c71
systemctl restart iscsi

yum -y install device-mapper-multipath
cp /usr/share/doc/device-mapper-multipath-0.4.9/multipath.conf /etc/
systemctl enable multipathd
systemctl start multipathd
lsmod |grep dm

iscsiadm -m iface -I eth0 -o new
iscsiadm -m iface -I eth1 -o new

iscsiadm -m discovery -t st -p IP
iscsiadm -m node -l
iscsiadm -m node -u
iscsiadm -m session

[root@c7-1 ~]# cd /var/lib/iscsi/
[root@c7-1 iscsi]# ls
ifaces  isns  nodes  send_targets  slp  static

fdisk -l |grep sd

multipath -ll

vi /etc/multipath.conf
multipaths {
        multipath {
                wwid                    364ed2aa51864120b621ab51c2b9bf4b1
                alias                   data
                path_grouping_policy    multibus
                failback                immediate
                rr_weight               priorities
                no_path_retry           5
        }
#       multipath {
#               wwid                    1DEC_____321816758474
#               alias                   red
#       }
}

multipath -v2
systemctl reload multipathd
multipath -ll

mkfs.xfs /dev/mapper/data

echo "/dev/mapper/data  /data  xfs  _netdev,discard,defaults 0 0" >> /etc/fstab

mount -a
```
