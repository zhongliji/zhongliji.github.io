### 由于向集群里面添加mediator时，不会自动生成证书，所以需要手动复制mediator证书到集群
# 查看mediator证书
```
cat /opt/netapp/lib/ontap_mediator/ontap_mediator/server_config/ca.crt
-----BEGIN CERTIFICATE-----
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
-----END CERTIFICATE-----
```

# 复制证书全部内容，安装到集群里面，两个集群都要安装
```
security certificate install -type server-ca -vserver cluster1
Please enter Certificate: Press <Enter> when done
复制证书内容到这里

security certificate install -type server-ca -vserver cluster2
```
# 集群添加mediator
```
snapmirror mediator add -mediator-address 192.168.6.240 -peer-cluster cluster2 -username mediatoradmin
snapmirror mediator show
```
