
# CNI

![](Images/Pasted%20image%2020230320073442.png)



<br/>
<br/>
<br/>
<br/>

# CNI comes with some default plugins like 

![](Images/Pasted%20image%2020230320073823.png)


<br/>
<br/>
<br/>
<br/>

# There are also 3rd party plugins available

![](Images/Pasted%20image%2020230320073905.png)








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



# Docker doesnt support CNI. Instead it has its own sstandarrds called Container Network Model CNM. Hence cant directly use cni plugins. 

Tho can use workaround for it by starting container with none network and manually invoking the bridge plugin for the ns 
![](Images/Pasted%20image%2020230320074401.png)


![](Images/Pasted%20image%2020230320074417.png)












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






# Default path configured for CNI supported plugin's binaries

**/opt/cni/bin**



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



# Path of CNI plugin configured for k8s cluster 
(only if --network-plugin=cni)
**/etc/cni/net.d/**






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






