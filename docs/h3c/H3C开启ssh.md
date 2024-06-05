# 1.SW上开启SSH服务 #
``` 
[SW]ssh server enable
```

# 2.配置ssh服务的用户名、密码 #
```
[SW]local-user admin
[SW-luser-manage-admin]password simple liyang@008
```

# 3.配置改用户的权限为网络管理级别 #
```
[SW-luser-manage-admin]authorization-attribute user-role network-admin
```

# 4.配置该用户可以管理的协议为ssh #
```
[SW-luser-manage-admin]service-type ssh
```

# 5.设置SSH客户端登录用户界面的认证方式为scheme方式 #
```
[SW]line vty 0 63
[SW-line-vty0-63]authentication-mode scheme
```