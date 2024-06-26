## list partition on node shell
```
cdlab::> run -node cdlab-01
cdlab-01> sysconfig -a
cdlab-01> sysconfig -r
cdlab-01> disk show -v
cdlab-01> disk show -n
cdlab-01> aggr status -r
cdlab-01> aggr status -s
cdlab-01> aggr status -f
```
## remove data partition on cluster shell advanced mode
```
cdlab::> disk option show -fields autoassign
cdlab::> disk option modify -node * -autoassign off
cdlab::> set adv
cdlab::*> storage disk show
cdlab::*> storage disk removeowner -disk 1.0.1 -data true
```
## assign data partitioning 
```
cdlab::> set adv
cdlab::*> disk assign -owner cdlab-01 -disk 1.0.1 -data true
cdlab::*> disk show -partition-ownership
```
## disk unpartition and ownership need spare disk
```
cdlab::> set adv
cdlab::*> storage disk removeowner -disk 1.0.11 -data true
cdlab::*> set adm
cdlab::> run -node cdlab-01
cdlab-01> disk assign 0b.00.11P1
cdlab-01> priv set advanced
cdlab-01*> disk unpartition 0b.00.11
cdlab-01*> disk remove_ownership 0b.00.11
cdlab-01*> priv set admin
cdlab-01> 
```
## not ADP
```
LOADER>setenv root-uses-shared-disks? false
LOADER>saveenv
```