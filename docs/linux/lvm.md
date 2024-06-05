## LVM 
```
pvs #view exist pv or pvdisplay view detail 
vgs #view exist vg or vgdisplay view detail 
lvs #view exist lv or lvdisplay view detail

pvcreate /dev/sdb #new pv
vgcreate vg01 /dev/sdb /dev/sdc #new vg
lvcreate -L 20G -n lv1 vg01 #new 20G size lv from vg01
lvcreate -l 100%free -n lv1 vg01 #new all PE from vg01

vgextend centos /dev/sdb #extend vg size
lvextend -l +100%free /dev/centos/root #extend lv PE to all vg size
lvextend -L +10g /dev/centos/root #extend lv add 10g size from vg

xfs_growfs /dev/centos/root #extend xfs filesystem at root part
resize2fs /dev/vg01/lv1 #extend ext4 filesystem online

edit /etc/lvm/lvm.config，modify locking_type and volume_list field
locking_type = 1
volume_list = [ "VolGroup" ] #only local vg

lvscan
lvchange -ay /dev/vg1/lv1
```
