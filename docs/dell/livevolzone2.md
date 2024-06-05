## liveVolume zone2 
```
G610-DOWN:admin> switchshow
switchName:	G610-DOWN
switchType:	170.0
switchState:	Online   
switchMode:	Native
switchRole:	Principal
switchDomain:	1
switchId:	fffc01
switchWwn:	10:00:88:94:71:96:a0:57
zoning:		ON (sc3020)
switchBeacon:	OFF
HIF Mode:	OFF

Index Port Address  Media Speed   State       Proto
==================================================
   0   0   010000   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   1   1   010100   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   2   2   010200   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   3   3   010300   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   4   4   010400   id    N16	  Online      FC  F-Port  10:00:00:10:9b:ad:f8:60 
   5   5   010500   id    N16	  Online      FC  F-Port  10:00:00:10:9b:ad:f8:09 
   6   6   010600   id    N16	  Online      FC  F-Port  10:00:00:10:9b:ad:f9:54 

G610-DOWN:admin> cfgshow
Defined configuration:
 cfg:	sc3020	phy_zone; sc3020a_v_zone; sc3020a_p_zone; sc3020b_v_zone; 
		sc3020b_p_zone; sc3020a; sc3020b; r740a_z_sc3020a; 
		r740a_z_sc3020b; r740b_z_sc3020a; r740b_z_sc3020b; 
		r740c_z_sc3020a; r740c_z_sc3020b

 zone:	phy_zone	
		sc3020a_up_p2; sc3020a_down_p2; sc3020b_up_p2; 
		sc3020b_down_p2

 zone:	sc3020a	sc3020a_up_p2; sc3020a_down_p2; sc3020b_up_v2; 
		sc3020b_down_v2

 zone:	sc3020b	sc3020b_up_p2; sc3020b_down_p2; sc3020a_up_v2; 
		sc3020a_down_v2

 zone:	sc3020a_p_zone	
		sc3020a_up_p2; sc3020a_down_p2
 zone:	sc3020a_v_zone	
		sc3020a_up_v2; sc3020a_down_v2

 zone:	sc3020b_p_zone	
		sc3020b_up_p2; sc3020b_down_p2
 zone:	sc3020b_v_zone	
		sc3020b_up_v2; sc3020b_down_v2

 zone:	r740a_z_sc3020a	
		r740a; sc3020a_up_v2; sc3020a_down_v2
 zone:	r740a_z_sc3020b	
		r740a; sc3020b_up_v2; sc3020b_down_v2

 zone:	r740b_z_sc3020a	
		r740b; sc3020a_up_v2; sc3020a_down_v2
 zone:	r740b_z_sc3020b	
		r740b; sc3020b_up_v2; sc3020b_down_v2

 zone:	r740c_z_sc3020a	
		r740c; sc3020a_up_v2; sc3020a_down_v2
 zone:	r740c_z_sc3020b	
		r740c; sc3020b_up_v2; sc3020b_down_v2

 alias:	r740a	10:00:00:10:9b:ad:f8:60
 alias:	r740b	10:00:00:10:9b:ad:f8:09
 alias:	r740c	10:00:00:10:9b:ad:f9:54

 alias:	sc3020a_down_p2	
		50:00:d3:10:03:d7:ca:1b
 alias:	sc3020a_down_v2	
		50:00:d3:10:03:d7:ca:34

 alias:	sc3020a_up_p2	
		50:00:d3:10:03:d7:ca:05
 alias:	sc3020a_up_v2	
		50:00:d3:10:03:d7:ca:33

 alias:	sc3020b_down_p2	
		50:00:d3:10:03:d7:cc:1b
 alias:	sc3020b_down_v2	
		50:00:d3:10:03:d7:cc:34

 alias:	sc3020b_up_p2	
		50:00:d3:10:03:d7:cc:05
 alias:	sc3020b_up_v2	
		50:00:d3:10:03:d7:cc:33

Effective configuration:
 cfg:	sc3020	
 zone:	phy_zone	
		50:00:d3:10:03:d7:ca:05
		50:00:d3:10:03:d7:ca:1b
		50:00:d3:10:03:d7:cc:05
		50:00:d3:10:03:d7:cc:1b
 zone:	r740a_z_sc3020a	
		10:00:00:10:9b:ad:f8:60
		50:00:d3:10:03:d7:ca:33
		50:00:d3:10:03:d7:ca:34
 zone:	r740a_z_sc3020b	
		10:00:00:10:9b:ad:f8:60
		50:00:d3:10:03:d7:cc:33
		50:00:d3:10:03:d7:cc:34
 zone:	r740b_z_sc3020a	
		10:00:00:10:9b:ad:f8:09
		50:00:d3:10:03:d7:ca:33
		50:00:d3:10:03:d7:ca:34
 zone:	r740b_z_sc3020b	
		10:00:00:10:9b:ad:f8:09
		50:00:d3:10:03:d7:cc:33
		50:00:d3:10:03:d7:cc:34
 zone:	r740c_z_sc3020a	
		10:00:00:10:9b:ad:f9:54
		50:00:d3:10:03:d7:ca:33
		50:00:d3:10:03:d7:ca:34
 zone:	r740c_z_sc3020b	
		10:00:00:10:9b:ad:f9:54
		50:00:d3:10:03:d7:cc:33
		50:00:d3:10:03:d7:cc:34
 zone:	sc3020a	50:00:d3:10:03:d7:ca:05
		50:00:d3:10:03:d7:ca:1b
		50:00:d3:10:03:d7:cc:33
		50:00:d3:10:03:d7:cc:34
 zone:	sc3020a_p_zone	
		50:00:d3:10:03:d7:ca:05
		50:00:d3:10:03:d7:ca:1b
 zone:	sc3020a_v_zone	
		50:00:d3:10:03:d7:ca:33
		50:00:d3:10:03:d7:ca:34
 zone:	sc3020b	50:00:d3:10:03:d7:cc:05
		50:00:d3:10:03:d7:cc:1b
		50:00:d3:10:03:d7:ca:33
		50:00:d3:10:03:d7:ca:34
 zone:	sc3020b_p_zone	
		50:00:d3:10:03:d7:cc:05
		50:00:d3:10:03:d7:cc:1b
 zone:	sc3020b_v_zone	
		50:00:d3:10:03:d7:cc:33
		50:00:d3:10:03:d7:cc:34
```