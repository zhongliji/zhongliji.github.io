## stack-group as port
```
stack-group 0 (port 1,2,3,4)
stack-group 1 (port 5,6,7,8)
stack-group 2 (port 9,10,11,12)
stack-group 3 (port 13,14,15,16)
stack-group 12 (port 49) 
stack-group 13 (port 50) 
stack-group 14 (port 51) 
stack-group 15 (port 52) 
stack-group 16 (port 53) 
stack-group 17 (port 54) 
```
## config stack on each switch
```
sw1:
stack-unit 1 priority 10
stack-unit 1 stack-group 12
stack-unit 1 stack-group 13
stack-unit 2 provision S4048-ON

sw2:
stack-unit 1 renumber 2 //need reboot
stack-unit 2 priority 5
stack-unit 2 stack-group 12
stack-unit 2 stack-group 13
stack-unit 1 provision S4048-ON
```
