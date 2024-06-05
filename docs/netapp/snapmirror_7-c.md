## enable snapmirror on 7mode
```
options snapmirror.enable on
options snapmirror.access all
```
## cmode create aggr
```
liyang::> aggr create -aggregate aggr1 -diskcount 12 -maxraidsize 12 -raidtype raid_dp -node liyang-02
```
## cmode create vserver
```
liyang::> vserver create -vserver svm1
```
## cmode create vol with type DP
```
liyang::> vol create -volume fas2240a_dr -vserver svm1 -aggregate aggr1 -size 20G -state online -type DP
```
## cmode create intercluster lif; -service-policy default-intercluster; on ontap 9.10
```
liyang::> net int create -vserver liyang -lif ic1 -role intercluster -address 192.168.8.111 -netmask 255.255.255.0 -home-node liyang-01 -home-port e0c -status-admin up
```
## create vserver peer transition
```
liyang::> vserver peer transition create -local-vserver svm1 -src-filer-name 192.168.6.211 -local-lifs ic1
```
## create snapmirror with type TDP
```
liyang::> snapmirror create -source-path 192.168.6.211:vol2 -destination-path svm1:fas2240a_dr -type TDP
```
## snapmirror initialize
```
liyang::> snapmirror initialize -destination-path svm1:fas2240a_dr
liyang::> snapmirror update -destination-path svm1:fas2240a_dr
liyang::> snapmirror quiesce -destination-path svm1:fas2240a_dr
liyang::> snapmirror resume -destination-path svm1:fas2240a_dr
liyang::> snapmirror break -destination-path svm1:fas2240a_dr
liyang::> snapmirror resync -destination-path svm1:fas2240a_dr
liyang::> snapmirror show
```