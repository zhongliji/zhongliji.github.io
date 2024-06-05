## 配置yum源 
```
vi /etc/yum.repo.d/tie.repo
[tie]
name=tie
baseurl=file:///root/tiebreaker
gpgcheck=0
enable=1

yum clean all
yum makecache
```

## 安装java 
```
yum install java-1.8.0-openjdk.x86_64
java -version
```

## 安装maridb 
```
yum install mariadb-server.x86_64
systemctl start mariadb
systemctl enable mariadb
mysql_secure_installation
```

## 安装tiebreaker 
```
rpm -ivh NetApp-MetroCluster-TiebreakerSoftware-1.21P2-1.x86_64.rpm
systemctl status netapp-metrocluster-tiebreaker-software
systemctl enable netapp-metrocluster-tiebreaker-software
```

## 配置 
```
netapp-metrocluster-tiebreaker-software-cli
monitor add wizard
monitor show -status

monitor modify -monitor-name monitor_name -observer-mode false
```