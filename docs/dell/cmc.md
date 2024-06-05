## CMC CLI
```
ssh connection cmcip #save session with root/calvin

然后依次运行以下命令：

getversion
getioinfo
getdcinfo
racdump
dumplogs
getsvctag

racadm help

$ help
 
 help            -- list racadm subcommand description
 help <subcommand> -- display usage summary for a subcommand
 ?               -- list racadm subcommand description
 ? <subcommand>  -- display usage summary for a subcommand
 arp             -- display the networking arp table
 chassisaction   -- execute chassis or switch power-up/down/cycle or KVM powercycle
 closessn        -- close a session
 clrraclog       -- clear the CMC log
 clrsel          -- clear the System Event Log (SEL)
 ifconfig        -- display network interface information

racadm getniccfg -m server-1

racadm setniccfg  -m server-1 -s 192.168.0.161 255.255.255.0 192.168.0.1

racadm setniccfg  -m server-1 -s 10.254.88.47 255.255.255.128 10.254.88.1

getioinfo
connect switch-a1
connect switch-a2

setniccfg -s 192.168.0.120 255.255.255.0 192.168.0.1  #set cmcIP

setniccfg -s 10.254.88.53 255.255.255.128 10.254.88.1

getniccfg  #get cmcIP
```