## login sp naroot for 7m; admin for cm;
```
system console 
ctrl+D
system power cycle
sp reboot
#login ontap
sp show
reboot-sp -node nodename
```
## unlock the diag user account on cmode
```
security login show
security login show -username diag
security login unlock -username diag
security login password -username diag
set -privilege diagnostic
systemshell -node nodename
```
## unlock the diag user account on 7mode
```
priv set advanced
useradmin diaguser show
useradmin diaguser unlock
useradmin diaguser show
useradmin diaguser password
priv set advanced
systemshell
```
## view sp mgwd process
```
sudo spmctl -l | grep mgwd
sudo rdb_dump Management
```
## stop sp mgwd process
```
sudo spmctl -s -h mgwd
sudo spmctl -s -h mgwd
```
## check sp mgwd process
```
sudo spmctl -l | grep mgwd
```
## reboot env_mgr process
```
sudo spmctl -sh env_mgr
sudo spmctl -eh env_mgr
sudo spmctl -l |grep env_mgr
```