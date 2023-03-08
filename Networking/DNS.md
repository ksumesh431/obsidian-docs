

# /etc/hosts

The local DNS entries
![](Images/Pasted%20image%2020230228025353.png)


<br/>


![](Images/Pasted%20image%2020230228025458.png)


<br/>


**These entries are specific for a particular host. In a network of multiple hosts, must add the entries in each host's files**.



**Instead we create a DNS server and point all requests to the DNS server**


**To point to a DNS server, have to add the dns server entry in `/etc/resolve.conf`**

![](Images/Pasted%20image%2020230228025843.png)

The 8.8.8.8 that is the google dns server can be added to our dns server so that any unknown entry can be forwarded to google's dns server
![](Images/Pasted%20image%2020230228030429.png)



<br/>



> By default, the local /etc/hosts entry takes precedence over DNS server entries. Can change that by editing the `/etc/nsswitch.conf` hosts entry.










<br/>
<br/>

---

---
<br/>
<br/>







# Domain Names



-   ` . ` is the root 
-   ` .com`  is the top level domain
-   `google` is the Domain Name assigned to it
-   `www`  is the subdomain (examples: maps, meet, mail etc.)


![](Images/Pasted%20image%2020230228030943.png)

Once resolved, the organisation's dns server cache's the ip address for a period of time (few secs - mins)




<br/>
<br/>

---

---
<br/>
<br/>





# Can also add search entry in resolv.conf for creating short names DNS.

For example a mycompany.com search entry will make sure that any "web" entry means web.mycompany.com
![](Images/Pasted%20image%2020230228031352.png)



<br/>
<br/>

---

---
<br/>
<br/>






# Record types

![](Images/Pasted%20image%2020230228031429.png)





<br/>
<br/>

---

---
<br/>
<br/>



# To lookup a hostname from a DNS server, can use `nslookup`
![](Images/Pasted%20image%2020230228031548.png)

> Tho it ignores the local /etc/hosts file

<br/>

### Same for `dig` command
It also looks up a hostname details from DNS server in more detail. tho ignores local hosts file
![](Images/Pasted%20image%2020230228031723.png)




<br/>
<br/>

---

---
<br/>
<br/>




# Setting up a server as dns server using CoreDNS

Download and run the coredns binary or docker file. add the entries in its /etc/hosts file and configure coredns to use that.

![](Images/Pasted%20image%2020230228032031.png)