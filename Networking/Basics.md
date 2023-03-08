
# Switching
Allows communication between devices on same network only

![](Images/Pasted%20image%2020230228021217.png)




<br/>
<br/>

---

---
<br/>
<br/>




# Router
Helps connect differnet networks

### Example

A router is connecting 2 networks. So it has 2 ip addresses that act as gateway.

To configure communication, 1st we add a route to the 1st network using `ip route add 192.168.2.0/24 via 192.168.1.1`  which adds a gateway route entry.

![](Images/Pasted%20image%2020230228021939.png)

Have to add entries like this for every system/network.

<br/>

For the Internet, can't add every website ip address, hence we specify that any unidentified network should passed to the router through the default gateway

![](Images/Pasted%20image%2020230228022731.png)

![](Images/Pasted%20image%2020230307080832.png)



<br/>
<br/>

---

---
<br/>
<br/>




# Setting up a linux host as a router


![](Images/Pasted%20image%2020230228023207.png)

This will work. But by default, Linux dosnt allow to forward packets from one interface to another like from eth0 to eth1 for security reasons.

For enabling this forwarding , set this file value to 1
![](Images/Pasted%20image%2020230228023358.png)

<br/>

To make this persist on reboot, change in 
![](Images/Pasted%20image%2020230228023428.png)





<br/>
<br/>

---

---
<br/>
<br/>



## To list or modify hosts `ip link`

**ip link lists the network interfaces like eth0 etc.**



<br/>
<br/>

## To see the assigned ip addresses or to add ip address `ip addr` and `ip addr add 192.168.1.10/24 dev eth0`

![](Images/Pasted%20image%2020230307072220.png)



<br/>
<br/>


## Changes made using abve cmnds dont persist on reboot. Must set in `/etc/network/interfaces` file





<br/>
<br/>




# To check routes or add routes
![](Images/Pasted%20image%2020230228023951.png)



<br/>
<br/>


# To check if ip forwarding is enabled, have to check
![](Images/Pasted%20image%2020230228024029.png)





<br/>
<br/>

---

---
<br/>
<br/>





## Routing Table vs ARP Table
> A routing table and an ARP (Address Resolution Protocol) table are both important components of a network's operation, but they serve different functions.

>A routing table is used by a router to determine the best path for forwarding data packets to their intended destinations. The routing table typically contains a list of networks and their associated next hop routers or interfaces. When a packet arrives at the router, the router checks the destination IP address against its routing table to determine where to send the packet next.

>On the other hand, an ARP table is used to map a device's IP address to its physical (MAC) address. When a device wants to send data to another device on the same network, it needs to know the physical address of the intended recipient. The device sends an ARP request to the network, asking for the MAC address that corresponds to a particular IP address. Once it receives the response, it updates its ARP table so that it can send data directly to the correct MAC address in the future.

>In summary, a routing table is used by routers to determine how to forward packets, while an ARP table is used by devices to map IP addresses to physical addresses for communication on the local network.




<br/>
<br/>

---

---
<br/>
<br/>




# To bring UP a network interface that is down

![](Images/Pasted%20image%2020230307073136.png)

