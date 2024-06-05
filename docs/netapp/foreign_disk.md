## check foreign information
```
disk show -v
```

## Force assign foreign disk to be unowend
```
priv set advanced
disk assign -s unowned diskname -f
```

## remove bad label
```
aggr status -f
priv set diag
disk unfail -s diskname
```