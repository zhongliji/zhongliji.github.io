## liveVolume zone1
```
G610-UP:admin> switchshow
switchName:	G610-UP
switchType:	170.0
switchState:	Online   
switchMode:	Native
switchRole:	Principal
switchDomain:	1
switchId:	fffc01
switchWwn:	10:00:88:94:71:ad:f7:20
zoning:		ON (sc3020)
switchBeacon:	OFF
HIF Mode:	OFF

Index Port Address  Media Speed   State       Proto
==================================================
   0   0   010000   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   1   1   010100   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   2   2   010200   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   3   3   010300   id    N16	  Online      FC  F-Port  1 N Port + 1 NPIV public 
   4   4   010400   id    N16	  Online      FC  F-Port  10:00:00:10:9b:ad:f8:61 
   5   5   010500   id    N16	  Online      FC  F-Port  10:00:00:10:9b:ad:f8:0a 
   6   6   010600   id    N16	  Online      FC  F-Port  10:00:00:10:9b:ad:f9:55 

G610-UP:admin> cfgshow
Defined configuration:
 cfg:	sc3020	phy_zone; sc3020a; sc3020b; sc3020a_p_zone; sc3020a_v_zone; 
		sc3020b_p_zone; sc3020b_v_zone; r740a_z_sc3020a; 
		r740a_z_sc3020b; r740b_z_sc3020a; r740b_z_sc3020b; 
		r740c_z_sc3020a; r740c_z_sc3020b

 zone:	phy_zone	
		sc3020a_up_p1; sc3020a_down_p1; sc3020b_up_p1; 
		sc3020b_down_p1

 zone:	sc3020a	sc3020a_up_p1; sc3020a_down_p1; sc3020b_up_v1; 
		sc3020b_down_v1

 zone:	sc3020b	sc3020b_up_p1; sc3020b_down_p1; sc3020a_up_v1; 
		sc3020a_down_v1

 zone:	sc3020a_p_zone	
		sc3020a_up_p1; sc3020a_down_p1
 zone:	sc3020a_v_zone	
		sc3020a_up_v1; sc3020a_down_v1

 zone:	sc3020b_p_zone	
		sc3020b_up_p1; sc3020b_down_p1
 zone:	sc3020b_v_zone	
		sc3020b_up_v1; sc3020b_down_v1

 zone:	r740a_z_sc3020a	
		r740a; sc3020a_up_v1; sc3020a_down_v1
 zone:	r740a_z_sc3020b	
		r740a; sc3020b_up_v1; sc3020b_down_v1

 zone:	r740b_z_sc3020a	
		r740b; sc3020a_up_v1; sc3020a_down_v1
 zone:	r740b_z_sc3020b	
		r740b; sc3020b_up_v1; sc3020b_down_v1

 zone:	r740c_z_sc3020a	
		r740c; sc3020a_up_v1; sc3020a_down_v1
 zone:	r740c_z_sc3020b	
		r740c; sc3020b_up_v1; sc3020b_down_v1

 alias:	r740a	10:00:00:10:9b:ad:f8:61
 alias:	r740b	10:00:00:10:9b:ad:f8:0a
 alias:	r740c	10:00:00:10:9b:ad:f9:55

 alias:	sc3020a_down_p1	
		50:00:d3:10:03:d7:ca:1c
 alias:	sc3020a_down_v1	
		50:00:d3:10:03:d7:ca:32

 alias:	sc3020a_up_p1	
		50:00:d3:10:03:d7:ca:06
 alias:	sc3020a_up_v1	
		50:00:d3:10:03:d7:ca:31

 alias:	sc3020b_down_p1	
		50:00:d3:10:03:d7:cc:1c
 alias:	sc3020b_down_v1	
		50:00:d3:10:03:d7:cc:32

 alias:	sc3020b_up_p1	
		50:00:d3:10:03:d7:cc:06
 alias:	sc3020b_up_v1	
		50:00:d3:10:03:d7:cc:31

Effective configuration:
 cfg:	sc3020	
 zone:	phy_zone	
		50:00:d3:10:03:d7:ca:06
		50:00:d3:10:03:d7:ca:1c
		50:00:d3:10:03:d7:cc:06
		50:00:d3:10:03:d7:cc:1c
 zone:	r740a_z_sc3020a	
		10:00:00:10:9b:ad:f8:61
		50:00:d3:10:03:d7:ca:31
		50:00:d3:10:03:d7:ca:32
 zone:	r740a_z_sc3020b	
		10:00:00:10:9b:ad:f8:61
		50:00:d3:10:03:d7:cc:31
		50:00:d3:10:03:d7:cc:32
 zone:	r740b_z_sc3020a	
		10:00:00:10:9b:ad:f8:0a
		50:00:d3:10:03:d7:ca:31
		50:00:d3:10:03:d7:ca:32
 zone:	r740b_z_sc3020b	
		10:00:00:10:9b:ad:f8:0a
		50:00:d3:10:03:d7:cc:31
		50:00:d3:10:03:d7:cc:32
 zone:	r740c_z_sc3020a	
		10:00:00:10:9b:ad:f9:55
		50:00:d3:10:03:d7:ca:31
		50:00:d3:10:03:d7:ca:32
 zone:	r740c_z_sc3020b	
		10:00:00:10:9b:ad:f9:55
		50:00:d3:10:03:d7:cc:31
		50:00:d3:10:03:d7:cc:32
 zone:	sc3020a	50:00:d3:10:03:d7:ca:06
		50:00:d3:10:03:d7:ca:1c
		50:00:d3:10:03:d7:cc:31
		50:00:d3:10:03:d7:cc:32
 zone:	sc3020a_p_zone	
		50:00:d3:10:03:d7:ca:06
		50:00:d3:10:03:d7:ca:1c
 zone:	sc3020a_v_zone	
		50:00:d3:10:03:d7:ca:31
		50:00:d3:10:03:d7:ca:32
 zone:	sc3020b	50:00:d3:10:03:d7:cc:06
		50:00:d3:10:03:d7:cc:1c
		50:00:d3:10:03:d7:ca:31
		50:00:d3:10:03:d7:ca:32
 zone:	sc3020b_p_zone	
		50:00:d3:10:03:d7:cc:06
		50:00:d3:10:03:d7:cc:1c
 zone:	sc3020b_v_zone	
		50:00:d3:10:03:d7:cc:31
		50:00:d3:10:03:d7:cc:32
```
