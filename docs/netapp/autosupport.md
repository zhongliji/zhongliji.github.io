## AUSP
```
autosupport invoke -node liyang-01 -type all -uri file:///mroot/etc/log/liyang_01.7z
autosupport invoke -node liyang-02 -type all -uri file:///mroot/etc/log/liyang_02.7z

system node autosupport history show -node liyang-01
system node autosupport history show -node liyang-02

https://ip/spi/
```

## 7-mode
```
options httpd.admin.enable on
options httpd.enable on
options httpd.autoindex.enable on
options httpd.rootdir /vol/vol0/etc/log

options autosupport.doit all
autosupport history show
autosupport history show -seq-num
autosupport history retransmit -seq-num 3148 -uri file:///mroot/etc/log/retransmit/zx-b-3148.7z
autosupport history show -seq-num 3148 retransmit
```