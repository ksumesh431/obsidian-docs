






# To create network Namespaces in linux

`ip netns add red`
`ip netns add blue`

To list the namespaces, `ip netns`


### To run the `ip link` command inside a network namespace
`ip -n NamespaceName link`
`ip -n red link`
OR `ip netns exec red ip link`

> any network command used after `ip netns exec nsName` will work for that network namespace



<br/>

### Similarily for `arp` command
![](Images/Pasted%20image%2020230307071758.png)

<br/>

### Same for `route` command
![](Images/Pasted%20image%2020230307071847.png)






<br/>
<br/>

---

---
<br/>
<br/>





# Connecting two network namespaces



## Create a pipe between two network ends 

![](Images/Pasted%20image%2020230307072617.png)



`ip link add veth-red type veth peer name veth-blue`

>-   `ip link add`: This specifies that we want to create a new network interface.
>-   `veth-red`: This is the name of the first virtual Ethernet interface that we want to create.
>-   `type veth`: This specifies that we want to create a virtual Ethernet interface.
>-   `peer name veth-blue`: This specifies that we want to create a second virtual Ethernet interface that is connected to the first interface. The second interface will be named `veth-blue`.


<br/>
<br/>
<br/>
<br/>


## Attach the created virtual interfaces to the namespaces

![](Images/Pasted%20image%2020230307072742.png)


<br/>
<br/>
<br/>
<br/>


## Add IP Address to each of the namespaces

![](Images/Pasted%20image%2020230307072859.png)


<br/>
<br/>
<br/>
<br/>




# Bring up the interfaces

![](Images/Pasted%20image%2020230307073210.png)

![](Images/Pasted%20image%2020230307073246.png)



<br/>
<br/>
<br/>
<br/>

## NOW BOTH THE NAMESPACES CAN COMMUNICATE WITH EACH OTHER




<br/>
<br/>
<br/>
<br/>

---

---
<br/>
<br/>
<br/>
<br/>



# Creating a virtual switch to connect more namespaces (using LINUX BRIDGE)

## First delete the pipe /link we created earlier

![](Images/Pasted%20image%2020230307074014.png)
**Deleting one end of the link deletes the other end automatically  **



<br/>
<br/>
<br/>
<br/>



## Create the virtual switch

It will be a normal network interface for the host. But will work as a switch for the namespaces
`ip link add v-net-0 type bridge`

It will be down on cretaing. Have to bring it up
`ip link set dev v-net-0 up`

![](Images/Pasted%20image%2020230307074401.png)



<br/>
<br/>
<br/>
<br/>


## Create pipe / links 

![](Images/Pasted%20image%2020230307074535.png)


<br/>
<br/>
<br/>
<br/>


## Set both ends for the red pipe. Attach one end to the red namespace and other end to the switch using "master"


![](Images/Pasted%20image%2020230307074639.png)




<br/>
<br/>
<br/>
<br/>


## Do similarily for blue network

![](Images/Pasted%20image%2020230307074813.png)



<br/>
<br/>
<br/>
<br/>




## Add ip addresses to the network interfaces

![](Images/Pasted%20image%2020230307074903.png)

<br/>

Also make sure to bring them up
![](Images/Pasted%20image%2020230307074937.png)


<br/>
<br/>
<br/>
<br/>




# The namespaces can now communicate with each other through the switch.

# We can't ping the namespace networks from the host yet. 

# To configure that, add ip address to the switch we created

We can add ip address to a switch as it is just another network interface for the host
`ip addr add 192.168.15.5/24 dev v-net-0` 

![](Images/Pasted%20image%2020230307080047.png)






<br/>
<br/>
<br/>
<br/>

---

---
<br/>
<br/>
<br/>
<br/>




# To enable connection from the **namespace** to reach a **private network** outside of our network


The localhost will work as a gateway between them
![](Images/Pasted%20image%2020230307081441.png)


<br/>
<br/>
<br/>
<br/>


### Add a route entry in the blue namespace 

This entry will route any traffic to the 192.168.1.0/24 network THROUGH THE GATEWAY NETWORK that is the SWITCH NETWORK.

![](Images/Pasted%20image%2020230307081922.png)




<br/>
<br/>
<br/>
<br/>


### Tho the ping will be incomplete as the outside network won't be able to reach back

To do that, Have to enable a NAT enabled on the Host acting as Gateway which will send messages to outside network with its own name nd address

<br/>

Add the NAT ip table entry for the SWITCH network to enable the NAT functionality
![](Images/Pasted%20image%2020230307082423.png)

![](Images/Pasted%20image%2020230307082737.png)

**Using POSTROUTING as packets will be going out from the NAT**

This way anyone recieving packets outside the network will see it coming from the Host network (192.168.1.2) instead of the namespace switch network.

<br/>

![](Images/Pasted%20image%2020230307082846.png)




<br/>
<br/>
<br/>
<br/>


# To enable the namespace connectivity to the Internet

The host can reach the Internet.

Have to add a default gateway which will route all external network requests through the host.

![](Images/Pasted%20image%2020230307083442.png)


<br/>
<br/>
<br/>
<br/>


# To enable the outside network to reach the internal namespace network


Can add a port forwarding NAT rule on the host taht wants to rech the namespace network.

![](Images/Pasted%20image%2020230307084307.png)

<br/>

![](Images/Pasted%20image%2020230307084115.png)



<br/>
<br/>

Now the external network can ping the blue namespace network
![](Images/Pasted%20image%2020230307084356.png)


