# show inode
```
vol show -volume vol01 -fields files
```

# modify for two method
```
vol modify -volume vol01 -files-set-maximum true

vol modify -volume vol01 -files 2490359
```