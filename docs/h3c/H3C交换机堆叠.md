## 堆叠是一种虚拟化技术。可以理解为把多台交换机虚拟化成一台大的交换机去使用 ##
#  1.将互联的接口shutdown #
```
<H3C>sys
[H3C]sysname sw1
[sw1]interface rang Ten-GigabitEthernet 1/0/49 to Ten-GigabitEthernet 1/0/51
[sw1-if-range]shutdown
[sw1-if-range]quit
[sw2]interface range Ten-GigabitEthernet 1/0/49 to Ten-GigabitEthernet 1/0/51
[sw2-if-range]shutdown
[sw2-if-range]quit
```
#  2.堆叠配置 #
```
sw1配置
[sw1]irf domain 100
[sw1]irf member 1 renumber 1
[sw1]irf-port 1/1
[sw1-irf-port1/1]port group interface Ten-GigabitEthernet 1/0/49
[sw1-irf-port1/1]port group interface Ten-GigabitEthernet 1/0/50
[sw1-irf-port1/1]port group interface Ten-GigabitEthernet 1/0/51
[sw1-irf-port1/1]quit
[sw1]irf-port-configuration active
sw2配置
[sw2]irf domain 100
[sw2]irf member 1 renumber 2
[sw2]irf-port 1/2
[sw2-irf-port1/2]port group interface Ten-GigabitEthernet 1/0/49
[sw2-irf-port1/2]port group interface Ten-GigabitEthernet 1/0/50
[sw2-irf-port1/2]port group interface Ten-GigabitEthernet 1/0/51
[sw2-irf-port1/2]quit
[sw2]irf-port-configuration active
save，reboot，堆叠生效
```

#  3.打开关闭的端口 #
```
[sw1]interface range Ten-GigabitEthernet 1/0/49 to Ten-GigabitEthernet 1/0/51
[sw1-if-range]undo shutdown
sw2会自动重启，重启后会发现sw2的名字变为sw1
修改优先级
[sw1]irf member 1 priority 3
```

#   4.验证 #
```
[sw1]dis interface brief
XGE1/0/49            UP   10G     F      --   --
XGE1/0/50            UP   10G     F      --   --
XGE1/0/51            UP   10G     F      --   --
XGE1/0/52            DOWN 10G     F      A    1
XGE2/0/49            UP   10G     F      --   --
XGE2/0/50            UP   10G     F      --   --
XGE2/0/51            UP   10G     F      --   --
XGE2/0/52            DOWN 10G     F      A    1

[sw1]dis current-configuration
#
 sysname sw1
#
 irf domain 100
 irf mac-address persistent timer
 irf auto-update enable
 undo irf link-delay
 irf member 1 priority 3
 irf member 2 priority 1
irf-port 1/1
 port group interface Ten-GigabitEthernet1/0/49
 port group interface Ten-GigabitEthernet1/0/50
 port group interface Ten-GigabitEthernet1/0/51
#
irf-port 2/2
 port group interface Ten-GigabitEthernet2/0/49
 port group interface Ten-GigabitEthernet2/0/50
 port group interface Ten-GigabitEthernet2/0/51

[sw1]dis irf
MemberID    Role    Priority  CPU-Mac         Description
 *+1        Master  3         6227-a30f-0104  ---
   2        Standby 1         6227-a977-0204  ---
--------------------------------------------------
 * indicates the device is the master.
 + indicates the device through which the user logs in.

 The bridge MAC of the IRF is: 6227-a30f-0100
 Auto upgrade                : yes
 Mac persistent              : 6 min
 Domain ID                   : 100
```