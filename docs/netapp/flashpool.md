## create flash pool for aggr with 4 ssd
```
aggregate modify -aggregate aggr_name -hybrid-enabled true
aggr add aggr_name -diskcount 3 -disktype SSD -raidtype raid4
```
## add new raidgroup to aggr with 8*8T HDD
```
aggr add aggr1 -diskcount 7 -disksize 8192 -raidgroup new -raidtype raid4
```