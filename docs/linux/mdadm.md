# install mdadm
```
yum -y install mdadm

[root@mdadm ~]# cat /proc/mdstat
Personalities : 
unused devices: <none>

[root@mdadm ~]# mdadm -C /dev/md0 -a yes -l 5 -n 3 -x 1 /dev/vd{b..e}
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.

[root@mdadm ~]# cat /proc/mdstat
Personalities : [raid6] [raid5] [raid4] 
md0 : active raid5 vdd[4] vde[3](S) vdc[1] vdb[0]
      585674752 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/2] [UU_]
      [>....................]  recovery =  3.5% (10351032/292837376) finish=71.8min speed=65499K/sec
      bitmap: 0/3 pages [0KB], 65536KB chunk

unused devices: <none>

[root@mdadm ~]# mdadm -D /dev/md0   #detail raid info

[root@mdadm ~]# mdadm -S /dev/md0   #stop raid
mdadm: stopped /dev/md0

[root@mdadm ~]# mdadm -As           #active raid
mdadm: /dev/md/0 has been started with 2 drives (out of 3), 1 rebuilding and 1 spare.

[root@mdadm ~]# mdadm --zero-superblock /dev/vd{b..e}   #delete raid
[root@mdadm ~]# mdadm -As
mdadm: No arrays found in config file or automatically

[root@mdadm ~]# mdadm -Es
ARRAY /dev/md/0  metadata=1.2 UUID=4d11c811:f7b8b31e:79612ecf:96439711 name=mdadm:0
   spares=1

[root@mdadm ~]# mdadm -S /dev/md0
mdadm: stopped /dev/md0

[root@mdadm ~]# mdadm -A /dev/md1 --uuid=4d11c811:f7b8b31e:79612ecf:96439711 /dev/vd{b..e}     #rename raid
mdadm: /dev/md1 has been started with 2 drives (out of 3), 1 rebuilding and 1 spare.

[root@mdadm ~]# cat /proc/mdstat
Personalities : [raid6] [raid5] [raid4] 
md1 : active raid5 vdb[0] vde[3](S) vdd[4] vdc[1]
      585674752 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/2] [UU_]
      [============>........]  recovery = 64.4% (188861604/292837376) finish=32.6min speed=53102K/sec
      bitmap: 0/3 pages [0KB], 65536KB chunk

unused devices: <none>

[root@mdadm mnt]# mdadm /dev/md1 -f /dev/vde -r /dev/vde   #fail disk remove disk

[root@mdadm mnt]# mdadm /dev/md1 -a /dev/vde               #add disk or spare disk

[root@mdadm mnt]# mdadm -D /dev/md1 |grep -i bitmap        #bitmap view

[root@mdadm mnt]# mdadm -G /dev/md1 --bitmap=internal      #set bitmap=internal

[root@mdadm mnt]# mdadm -G /dev/md1 --bitmap=none	   #set bitmap=none

[root@mdadm ~]# watch 'cat /proc/mdstat'
```
