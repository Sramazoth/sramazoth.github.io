---
layout: post
title: Setting and using scapy mitm
description: How to setup and use a mitm with scapy.
summary: How to setup and use a mitm with scapy.
tags: network python
---

# requirements
1. two network interfaces:
```sh
ip a
	1: lo: 
		[ . . . ]
	    
	2: eth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
	    link/ether 50:81:40:78:96:ac brd ff:ff:ff:ff:ff:ff
	3: eth1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
	    link/ether 50:81:40:88:85:bd brd ff:ff:ff:ff:ff:ff
```

2. `scapy` is installed and working (*already done if using kali*)

3. two equipments talking to each other using a **physical network** (*server and client on the schema*), I'm not doing wireless mitm here

4. (bonus) it is needed to disable `NetworkManager` to avoid interfaces to go off:
```sh
sudo systemctl disable NetworkManager
sudo systemctl stop NetworkManager
```

# schema
![mitm](/images/mitm.png)
(*I'm good at drawing garbage schemas*)

# physical steps
1. disconnect the physical connections between the server and the client
2. connect both the server and the client to the interfaces of the mitm equipment
3. verify connectivity with both interfaces, it should be something like:
```sh
ip a
	1: lo: 
		[ . . . ]
	    
	2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
	    link/ether 50:81:40:78:96:ac brd ff:ff:ff:ff:ff:ff
	3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
	    link/ether 50:81:40:88:85:bd brd ff:ff:ff:ff:ff:ff
```

# software steps
1. write a script to use scapy mitm:

```py
#!/usr/bin/python

from scapy.all import *

def modifyClientResponse(pkt):

	# do stuff with packet

	return pkt

def serverToClient(pkt):
	pkt.show()
	return True # return packet without modification

def clientToServer(pkt):
	pkt.show()
	if UDP in pkt: # if packet has layer UDP send packet to packet modifier 
		return modifyClientResponse(pkt)
	return True # return packet without modification

if __name__ == "__main__":
	# interfaces, based on schema
	interface_connected_to_server = "eth0"
	interface_connected_to_client = "eth1"
	bridge_and_sniff(
		if1=interface_connected_to_server, 
		if2=interface_connected_to_client, 
		xfrm12=serverToClient, # function to call for packets coming from if1 to if1
		xfrm21=clientToServer) # function to call for packets coming from if1 to if1
```

the mitm is now ready as we are using the function `bridge_and_sniff` ([official documentation](https://scapy.readthedocs.io/en/latest/api/scapy.sendrecv.html#scapy.sendrecv.bridge_and_sniff)), which simply get the packets and pass them as arguments to functions depending of the source interface

2. launching the script with sudo rights:
```sh
sudo python_mitm.py
```
it should be working fine

